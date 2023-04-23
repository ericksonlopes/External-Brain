```python
import logging
from datetime import datetime

from elasticsearch import Elasticsearch


class ElasticSearchLogger(logging.Logger):
    def __init__(self):
        self.index_name = "api_logs"
        super().__init__(self.index_name)

        self.__es = Elasticsearch([{'host': 'localhost', 'port': 9200, 'scheme': 'http'}])

        if not self.__es.indices.exists(index=self.index_name):
            self.__es.indices.create(index=self.index_name)

    def log(self, level, message, *args, **kwargs):
        message['level'] = level
        message["timestamp"] = datetime.now()

        self.__es.index(index=self.index_name, document=message)
        super().log(level, message)

    def info(self, message, *args, **kwargs):
        self.log(logging.INFO, message)

    def error(self, message, *args, **kwargs):
        self.log(logging.ERROR, message)

    def warning(self, message, *args, **kwargs):
        self.log(logging.WARNING, message)

    def critical(self, message, *args, **kwargs):
        self.log(logging.CRITICAL, message)

    def debug(self, message, *args, **kwargs):
        self.log(logging.DEBUG, message)


es_logger = ElasticSearchLogger()
es_logger.error({'message': 'Hello World!'})
```

Este código implementa um logger personalizado que registra mensagens de log em um índice do Elasticsearch. Ele estende a classe logging.Logger para fornecer os níveis de log padrão (INFO, WARNING, ERROR, CRITICAL e DEBUG) e inclui um método personalizado log que permite que o usuário registre mensagens de log personalizadas.

Ao criar uma instância do ElasticSearchLogger, um índice do Elasticsearch é criado se ainda não existir. O nome do índice é definido como "api_logs". Além disso, uma conexão com o Elasticsearch é estabelecida usando o host localhost e a porta 9200.

O método log adiciona informações adicionais à mensagem de log, como o nível de log e o carimbo de data/hora atual. A mensagem é, então, indexada no Elasticsearch usando o índice "api_logs".

Por fim, são fornecidos métodos personalizados (info, error, warning, critical e debug) que chamam o método log com o nível de log apropriado. O método log é chamado dentro desses métodos personalizados com a mensagem fornecida pelo usuário.

Na última linha do código, uma instância de ElasticSearchLogger é criada e uma mensagem de erro de teste é registrada no índice do Elasticsearch usando o método error. A mensagem de erro registrada inclui uma chave "message" com o valor "Hello World!".