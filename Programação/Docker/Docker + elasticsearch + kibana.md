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

