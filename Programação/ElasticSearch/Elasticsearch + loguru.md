


```python
from elasticsearch import Elasticsearch  
from loguru import logger  
  
es = Elasticsearch([{'host': 'localhost', 'port': 9200, 'scheme': 'http'}])  
  
index_name = 'api_logs'  
  
if not es.indices.exists(index=index_name):  
es.indices.create(index=index_name)  
  
  
class ElasticHandler:  
@staticmethod  
def write(message):  
es.index(index=index_name, document=message)  
  
  
elastic_handler = ElasticHandler()  
  
logger.add(elastic_handler)  
  
logger.info({'message': 'Hello World!'})
```

Este código demonstra como configurar o `Loguru` (um framework de logging) para enviar registros de log para o Elasticsearch.

O código começa importando as bibliotecas necessárias, incluindo o `Elasticsearch` do Python e o `logger` do `Loguru`.

Em seguida, é criado um objeto Elasticsearch com as informações de host, porta e esquema. O índice index_name é definido como 'api_logs' e é criado caso não exista.

Depois, a classe `ElasticHandler` é definida, que contém um método estático `write` que envia um registro de log (`message`) para o Elasticsearch usando o método `index()` do objeto Elasticsearch.

A instância `elastic_handler` da classe `ElasticHandler` é criada e adicionada ao logger usando o método `add()`, para que todos os logs registrados pelo logger sejam enviados para o Elasticsearch.

Finalmente, um registro de log de nível `info` é enviado ao logger contendo uma mensagem "Hello World!". Esse registro de log será então enviado para o Elasticsearch pelo `ElasticHandler`.

Ao executar este código, você verá um registro de log na sua instância do Elasticsearch com a mensagem "Hello World!" e outros detalhes, como data/hora, nível de registro, etc. Essa configuração pode ser expandida e personalizada para atender às suas necessidades de logging e análise de dados.

#elasticsearch #loguru #logs