```yaml
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

Este código é um arquivo de configuração em formato YAML para o docker-compose que define dois serviços, `elastic` e `kibana`, que são usados em conjunto para implantar e executar o Elasticsearch e o Kibana em contêineres Docker.

O serviço `elastic` usa a imagem do Docker Elasticsearch versão 8.6.2 e é definido com um nome de contêiner `elasticsearch`. Ele é configurado para reiniciar automaticamente, caso pare por algum motivo. O serviço tem um conjunto de variáveis ​​de ambiente definidas para configurar o Elasticsearch, incluindo a configuração de um único nó, a desativação da segurança xpack, o nome do cluster Elasticsearch, a memória máxima e mínima alocada para o Elasticsearch e a configuração para bloquear a memória na memória do host. A porta 9200 do contêiner é mapeada para a porta 9200 do host.

O serviço `kibana` usa a imagem do Docker Kibana versão 8.6.2 e é definido com um nome de contêiner `kibana`. Ele também é configurado para reiniciar automaticamente, caso pare por algum motivo. O serviço tem uma variável ​​de ambiente `ELASTICSEARCH_URL` definida para a URL do Elasticsearch. O serviço depende do serviço `elastic` e a porta 5601 do contêiner é mapeada para a porta 5601 do host.

#docker #elasticsearch #kibana #logging #docker-compose #loguru
