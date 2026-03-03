
Esta classe Python fornece uma maneira centralizada e configurável de gerenciar logs, gerando simultaneamente dois arquivos: um em formato de texto legível por humanos e outro em formato JSON para fácil ingestão por ferramentas de análise (como o ELK Stack).

## 🛠️ Funcionalidades

- **Múltiplos Handlers**: Salva logs em formato padrão e JSON.
- **Criação Automática de Pastas**: Garante que o diretório `logs/` exista na raiz do projeto.
- **Rastreabilidade**: Inclui nome do arquivo, número da linha e nome da função em cada log.

## 📄 Código da Classe

```python
import os
import logging

class Logger:
    # Caminho absoluto para a pasta de logs (ajustável conforme a estrutura)
    __ABSOLUTE_PATH = os.path.abspath(os.path.join(os.path.dirname(__file__), '../../logs'))

    def __init__(self, log_level="DEBUG"):
        self.logger = logging.getLogger(__name__)
        self.setup_logger(log_level)

    def setup_logger(self, log_level):
        os.makedirs(self.__ABSOLUTE_PATH, exist_ok=True)

        log_file = os.path.join(self.__ABSOLUTE_PATH, 'logs.log')
        json_log_file = os.path.join(self.__ABSOLUTE_PATH, 'logs_json.log')

        # Configurar o handler para o arquivo de log de texto
        file_handler = logging.FileHandler(log_file)
        file_handler.setLevel(log_level)
        formatter = logging.Formatter(
            '%(asctime)s | %(levelname)s | %(filename)s:%(lineno)d | %(funcName)s | %(message)s')
        file_handler.setFormatter(formatter)
        self.logger.addHandler(file_handler)

        # Configurar o handler para o arquivo de log JSON
        json_file_handler = logging.FileHandler(json_log_file)
        json_file_handler.setLevel(log_level)
        json_formatter = logging.Formatter(
            '{"time": "%(asctime)s", "level": "%(levelname)s", "filename": "%(filename)s", "line": %(lineno)d, '
            '"funcName": "%(funcName)s", "message": "%(message)s"}')
        json_file_handler.setFormatter(json_formatter)
        self.logger.addHandler(json_file_handler)

        self.logger.setLevel(log_level)

# Instância pronta para uso
logger = Logger(log_level="INFO").logger
```

## 🚀 Como Usar

Para usar o logger em qualquer módulo, basta importá-lo:

```python
from my_logger import logger

logger.info("Iniciando processo de ingestão de dados")
logger.error("Falha ao conectar ao banco de dados")
```

---
#python #logging #json #backend #data-engineering
