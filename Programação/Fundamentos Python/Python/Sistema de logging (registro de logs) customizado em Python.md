# 📋 Sistema de Logging Customizado em Python

Módulo de logging padronizado que encapsula o `logging` nativo do Python com interface limpa (`ILogger`), detecção automática do caller (arquivo, linha, classe, função) e filtragem de níveis via variável de ambiente.

## 🏗️ Arquitetura

```
┌─────────────┐
│   ILogger   │  ← Interface abstrata (ABC)
│  (contrato) │
└──────┬──────┘
       │ implements
┌──────▼──────┐
│  StdLogger  │  ← Implementação concreta
│  (logging)  │
└──────┬──────┘
       │ uses
┌──────▼──────────┐
│  _LoggerUtils   │  ← Utilitários (detecção de caller, níveis permitidos)
└─────────────────┘
```

## ⚙️ Configuração

A filtragem de níveis é controlada pela variável de ambiente `LIST_LOG_LEVELS`:

```bash
# Padrão (todos os níveis)
LIST_LOG_LEVELS="INFO,ERROR,CRITICAL,WARNING,DEBUG"

# Apenas erros e acima
LIST_LOG_LEVELS="ERROR,CRITICAL"
```

## 📄 Implementação

```python
import functools
import inspect
import logging
import os
import sys
from abc import abstractmethod, ABC
from datetime import datetime
from typing import Any

LOG_FORMAT = "{asctime} | {levelname} | {filepath}:{lineno} | {message}"

_LEVEL_MAP: dict[str, int] = {
    "DEBUG": logging.DEBUG,
    "INFO": logging.INFO,
    "WARNING": logging.WARNING,
    "ERROR": logging.ERROR,
    "CRITICAL": logging.CRITICAL,
}


class ILogger(ABC):
    """Interface abstrata — contrato para qualquer implementação de logger."""

    @abstractmethod
    def info(self, message: str, context: dict[str, Any] | None = None) -> None: ...

    @abstractmethod
    def debug(self, message: str, context: dict[str, Any] | None = None) -> None: ...

    @abstractmethod
    def warning(self, message: str, context: dict[str, Any] | None = None) -> None: ...

    @abstractmethod
    def error(self, error: Exception, context: dict[str, Any] | None = None) -> None: ...

    @abstractmethod
    def critical(self, message: str, context: dict[str, Any] | None = None) -> None: ...


class _LoggerUtils:
    """Utilitários internos para a infraestrutura de logging."""

    @staticmethod
    @functools.lru_cache(maxsize=1)
    def get_module_files(base_dir: str | None = None) -> frozenset[str]:
        """
        Retorna frozenset com caminhos absolutos de todos os .py do diretório do logger.
        Cacheado na primeira chamada para evitar walks repetidos no filesystem.
        """
        if base_dir is None:
            base_dir = os.path.dirname(os.path.abspath(__file__))

        logger_files: set[str] = set()
        for root, _, files in os.walk(base_dir):
            for file in files:
                if file.endswith('.py'):
                    logger_files.add(os.path.abspath(os.path.join(root, file)))
        return frozenset(logger_files)

    @staticmethod
    def get_log_record(level: str, message: str) -> dict[str, object]:
        """
        Extrai contexto do frame onde o log foi chamado originalmente.
        Pula frames originados de arquivos da infraestrutura do logger.
        """
        logger_files = _LoggerUtils.get_module_files()
        stack = inspect.stack()
        cls_name = ""
        frame_best = stack[1]

        for frame_info in stack:
            filename_abs = os.path.abspath(frame_info.filename)
            if filename_abs not in logger_files:
                self_obj = frame_info.frame.f_locals.get('self')
                if self_obj:
                    cls_name = type(self_obj).__name__
                frame_best = frame_info
                break

        asctime = datetime.now().strftime('%Y-%m-%d %H:%M:%S,%f')[:-3]

        return {
            'asctime': asctime,
            'levelname': level.upper(),
            'filename': os.path.basename(frame_best.filename),
            'filepath': os.path.abspath(frame_best.filename),
            'lineno': frame_best.lineno,
            'class': cls_name,
            'funcName': frame_best.function,
            'message': message,
        }

    @staticmethod
    def get_allowed_levels() -> list[str]:
        """Retorna níveis permitidos a partir da env var LIST_LOG_LEVELS."""
        raw = os.environ.get("LIST_LOG_LEVELS", "INFO,ERROR,CRITICAL,WARNING,DEBUG")
        if not raw:
            return list(_LEVEL_MAP.keys())

        return [
            name.strip().upper()
            for name in raw.split(",")
            if name.strip().upper() in _LEVEL_MAP
        ]


class StdLogger(ILogger):
    """Implementação concreta usando logging nativo do Python."""

    def __init__(self, log_format: str, name: str = "std-logger") -> None:
        self._log_format = log_format
        self._service_name = name
        self._allowed_levels = _LoggerUtils.get_allowed_levels()

        self._logger = logging.getLogger(f"std_logger_{self._service_name}")
        self._logger.handlers.clear()
        self._logger.setLevel(logging.DEBUG)
        self._logger.propagate = False

        handler = logging.StreamHandler(sys.stdout)
        handler.setLevel(logging.NOTSET)
        handler.setFormatter(logging.Formatter("%(message)s"))
        self._logger.addHandler(handler)

    def _is_allowed(self, level_name: str) -> bool:
        return level_name.upper() in self._allowed_levels

    def _log(self, level: str, message: str, context: dict[str, Any] | None = None) -> None:
        if not self._is_allowed(level):
            return

        msg = message
        if context:
            msg += f" | context={context}"

        record = _LoggerUtils.get_log_record(level, msg)
        formatted = self._log_format.format(**record)

        emit = getattr(self._logger, level.lower(), self._logger.info)
        emit(formatted)

    def info(self, message: str, context: dict[str, Any] | None = None) -> None:
        self._log("INFO", message, context)

    def debug(self, message: str, context: dict[str, Any] | None = None) -> None:
        self._log("DEBUG", message, context)

    def warning(self, message: str, context: dict[str, Any] | None = None) -> None:
        self._log("WARNING", message, context)

    def error(self, error: Exception, context: dict[str, Any] | None = None) -> None:
        self._log("ERROR", str(error), context)

    def critical(self, message: str, context: dict[str, Any] | None = None) -> None:
        self._log("CRITICAL", message, context)


# Singleton module-level
logger: ILogger = StdLogger(LOG_FORMAT)
```

## 🚀 Uso

```python
from src.config.logger import logger

# Logs simples
logger.info("Servidor iniciado na porta 8080")
logger.debug("Variáveis carregadas", context={"env": "production"})
logger.warning("Cache expirado, recarregando dados")

# Erros com exceção
try:
    result = 1 / 0
except Exception as e:
    logger.error(e, context={"operation": "divisão"})

# Log crítico
logger.critical("Banco de dados indisponível")
```

## 📤 Saída Esperada

```
2026-03-04 14:10:32,451 | INFO | C:\app\main.py:15 | Servidor iniciado na porta 8080
2026-03-04 14:10:32,452 | DEBUG | C:\app\main.py:16 | Variáveis carregadas | context={'env': 'production'}
2026-03-04 14:10:32,453 | ERROR | C:\app\main.py:21 | division by zero | context={'operation': 'divisão'}
```

## 🔑 Componentes Principais

| Componente | Responsabilidade |
|---|---|
| `ILogger` | Contrato abstrato — permite trocar a implementação (ex: para Loguru, Datadog) |
| `_LoggerUtils` | Detecção do caller real via `inspect.stack()`, filtragem de níveis |
| `StdLogger` | Implementação concreta com `logging` nativo, formatação customizada |
| `LOG_FORMAT` | Template da mensagem: `{asctime} \| {levelname} \| {filepath}:{lineno} \| {message}` |

## 💡 Destaques da Implementação

1. **Detecção automática do caller**: O `get_log_record` percorre a stack e ignora frames internos do logger, mostrando sempre o arquivo/linha de quem chamou.
2. **Cache com `lru_cache`**: A lista de arquivos do módulo do logger é escaneada uma única vez e cacheada.
3. **Filtragem por env var**: `LIST_LOG_LEVELS` permite ativar/desativar níveis sem alterar código.
4. **Sem dependências externas**: Usa apenas `logging`, `inspect` e `functools` da stdlib.

## 📝 Notas

- Para uma versão com Singleton thread-safe e toggle debug on/off, veja [[logging com debug on e off]].
- O `logger` é exportado como singleton no nível do módulo — basta importar e usar.

---
#python #logging #observability #clean-architecture #stdlib