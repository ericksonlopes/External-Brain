```python
import os  
  
import logging  
  
  
class Logger:  
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
  
        # Configurar o formato do log para incluir informações sobre a linha do código  
        formatter = logging.Formatter(  
            '%(asctime)s - %(levelname)s - %(filename)s:%(lineno)d - %(funcName)s - %(message)s')  
        file_handler.setFormatter(formatter)  
  
        # Adicionar o handler ao logger  
        self.logger.addHandler(file_handler)  
  
        # Configurar o handler para o arquivo de log JSON  
        json_file_handler = logging.FileHandler(json_log_file)  
        json_file_handler.setLevel(log_level)  
  
        # Configurar o formato do log JSON  
        json_formatter = logging.Formatter(  
            '{"time": "%(asctime)s", "level": "%(levelname)s", "filename": "%(filename)s", "line": %(lineno)d, '  
            '"funcName": "%(funcName)s", "message": "%(message)s"}')  
        json_file_handler.setFormatter(json_formatter)  
  
        # Adicionar o handler ao logger  
        self.logger.addHandler(json_file_handler)  
  
        # Definir o nível de log global do logger  
        self.logger.setLevel(log_level)  
  

logger = Logger(log_level="INFO").logger
```