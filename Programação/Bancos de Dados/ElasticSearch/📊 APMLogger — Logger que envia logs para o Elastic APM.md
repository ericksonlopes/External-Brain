# 📡 APMLogger — Logger que Envia Logs para o Elastic APM

Implementação da interface `ILogger` que envia logs diretamente para o **Elastic APM** via `capture_message()`. Permite centralizar logs da aplicação no Kibana/APM junto com traces e métricas.

## 🏗️ Arquitetura

```
┌─────────────┐
│   ILogger   │  ← Interface abstrata (contrato)
└──────┬──────┘
       │ implements
┌──────▼──────┐       ┌─────────────────┐
│  APMLogger  │──────▶│  Elastic APM    │  ← Logs enviados via capture_message()
└──────┬──────┘       │  (Kibana/APM)   │
       │ uses         └─────────────────┘
┌──────▼──────────┐
│  _LoggerUtils   │  ← Detecção de caller, níveis permitidos
└─────────────────┘
```

> O `APMLogger` é uma implementação **alternativa** ao `StdLogger`. Ambos seguem o contrato `ILogger`, permitindo trocar a estratégia de logging sem alterar o código da aplicação.

## 🔧 Dependências

```bash
pip install elastic-apm
```

## 📄 Implementação

```python
from typing import Optional, Dict
import logging
from elasticapm import Client

from src.domain.services.logger import ILogger
from src.infrastructure.logger.util import get_log_record
from src.config.logging_config import get_allowed_levels


class APMLogger(ILogger):
    """Logger que envia mensagens para o Elastic APM via capture_message()."""

    def __init__(self, log_format: str, client: Client) -> None:
        self.log_format = log_format
        self._client = client
        self.allowed_levels = get_allowed_levels()

    def _is_allowed(self, level_name: str) -> bool:
        try:
            if not self.allowed_levels:
                return False
            level = getattr(logging, level_name.upper(), None)
            return level in self.allowed_levels
        except Exception:
            return True

    def info(self, message: str, context: Optional[Dict] = None) -> None:
        if self._client and self._is_allowed('INFO'):
            msg = message
            if context:
                msg += f" | context={context}"
            ctx = get_log_record('INFO', msg)
            out = self.log_format.format(**ctx)
            self._client.capture_message(out, level="info")

    def debug(self, message: str, context: Optional[Dict] = None) -> None:
        if self._client and self._is_allowed('DEBUG'):
            msg = message
            if context:
                msg += f" | context={context}"
            ctx = get_log_record('DEBUG', msg)
            out = self.log_format.format(**ctx)
            self._client.capture_message(out, level="debug")

    def warning(self, message: str, context: Optional[Dict] = None) -> None:
        if self._client and self._is_allowed('WARNING'):
            msg = message
            if context:
                msg += f" | context={context}"
            ctx = get_log_record('WARNING', msg)
            out = self.log_format.format(**ctx)
            self._client.capture_message(out, level="warning")

    def error(self, error: Exception, context: Optional[Dict] = None) -> None:
        if self._client and self._is_allowed('ERROR'):
            msg = f"{error}"
            if context:
                msg += f" | context={context}"
            ctx = get_log_record('ERROR', msg)
            out = self.log_format.format(**ctx)
            self._client.capture_message(out, level="error")

    def critical(self, message: str, context: Optional[Dict] = None) -> None:
        if self._client and self._is_allowed('CRITICAL'):
            msg = message
            if context:
                msg += f" | context={context}"
            ctx = get_log_record('CRITICAL', msg)
            out = self.log_format.format(**ctx)
            self._client.capture_message(out, level="critical")
```

## 🚀 Uso

```python
from elasticapm import Client

LOG_FORMAT = "{asctime} | {levelname:<8} | {filename}:{caller}:{lineno} | {message}"

# Criar o client APM (ver nota sobre ElasticAPMManager)
apm_client = Client(
    service_name="minha-api",
    server_url="http://localhost:8200",
    environment="production",
)

# Instanciar o APMLogger
logger = APMLogger(LOG_FORMAT, client=apm_client)

# Usar exatamente como o StdLogger
logger.info("Servidor iniciado", context={"port": 8080})
logger.error(Exception("Falha na conexão com DB"), context={"db": "postgres"})
```

## ⚙️ Como Funciona

| Etapa | Descrição |
|---|---|
| 1. Verificação | `_is_allowed()` checa se o nível está na lista de níveis permitidos |
| 2. Formatação | `get_log_record()` extrai caller real (arquivo, linha, classe, função) |
| 3. Template | A mensagem é formatada usando o `log_format` |
| 4. Envio | `capture_message()` envia a mensagem formatada para o servidor APM |

## 🔄 Comparação com StdLogger

| Característica | `StdLogger` | `APMLogger` |
|---|---|---|
| Destino | Console (stdout) + arquivo local | Elastic APM (remoto) |
| Dependência | Apenas stdlib | `elastic-apm` |
| Visibilidade | Terminal / arquivo de log | Kibana APM Dashboard |
| Ideal para | Desenvolvimento local, CI/CD | Produção com observabilidade |

## 📝 Notas

- Se o `client` for `None`, nenhum log é enviado (fail-safe).
- Reutiliza os mesmos utilitários (`get_log_record`, `get_allowed_levels`) do [[📝 Sistema de logging (registro de logs) customizado em Python|StdLogger]].
- Para configurar o client APM com FastAPI, veja [[🔌 Elastic APM Client — Singleton Thread-Safe com FastAPI]].
- Pode ser combinado com `StdLogger` usando um **CompositeLogger** para logar simultaneamente no console e no APM.

---
#python #elasticsearch #apm #logging #observability
