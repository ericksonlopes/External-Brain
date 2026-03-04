#python #elasticsearch #kibana #docker #docker-compose

```yml
version: '3.9'

services:

  elastic:
    image: elasticsearch:8.6.2
    container_name: elasticsearch
    restart: unless-stopped
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - cluster.name=elasticsearch
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    ports:
      - "9200:9200"

  kibana:
    image: kibana:8.6.2
    container_name: kibana
    restart: unless-stopped
    environment:
      ELASTICSEARCH_URL: ${ELASTICSEARCH_URL}
    depends_on:
      - elastic
    ports:
      - "5601:5601"
```

O código é um arquivo docker-compose.yml que define a configuração de dois serviços: Elasticsearch e Kibana.

O serviço elastic usa a imagem elasticsearch:8.6.2, tem o nome do container definido como elasticsearch, é configurado para reiniciar sempre que ocorrer uma falha (restart: unless-stopped). As variáveis de ambiente definidas são: discovery.type=single-node, xpack.security.enabled=false, cluster.name=elasticsearch, bootstrap.memory_lock=true, e ES_JAVA_OPTS=-Xms512m -Xmx512m. A memória disponível para o container é ajustada usando ulimit com valores soft e hard definidos como -1. A porta 9200, que é a porta padrão do Elasticsearch, é mapeada para a porta 9200 da máquina host.

O serviço kibana usa a imagem kibana:8.6.2, tem o nome do container definido como kibana, é configurado para reiniciar sempre que ocorrer uma falha (restart: unless-stopped). A variável de ambiente definida é ELASTICSEARCH_URL: ${ELASTICSEARCH_URL} que é uma variável de ambiente usada pelo Kibana para se conectar ao Elasticsearch. O Elasticsearch é definido como dependência do Kibana (depends_on: elastic). A porta 5601, que é a porta padrão do Kibana, é mapeada para a porta 5601 da máquina host.

Este arquivo docker-compose.yml pode ser executado usando o comando docker-compose up -d para iniciar os serviços Elasticsearch e Kibana em segundo plano.


