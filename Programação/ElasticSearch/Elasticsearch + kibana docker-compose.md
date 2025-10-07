#elasticsearch #kibana #docker-compose 


```d
version: "3.9"  
  
services:  
  setup:  
    image: docker.elastic.co/elasticsearch/elasticsearch:8.15.2  
    environment:  
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}  
      - KIBANA_PASSWORD=${KIBANA_PASSWORD}  
    container_name: setup  
    command:  
      - bash  
      - -c  
      - |  
        echo "Waiting for Elasticsearch availability";  
        until curl -s http://elasticsearch:9200 | grep -q "missing authentication credentials"; do sleep 30; done;        echo "Setting kibana_system password";        until curl -s -X POST -u "elastic:${ELASTIC_PASSWORD}" -H "Content-Type: application/json" http://elasticsearch:9200/_security/user/kibana_system/_password -d "{\"password\":\"${KIBANA_PASSWORD}\"}" | grep -q "^{}"; do sleep 10; done;  
        echo "All done!";    networks:  
      - elasticnet  
  
  elasticsearch:  
    image: docker.elastic.co/elasticsearch/elasticsearch:8.15.2  
    container_name: elasticsearch  
    environment:  
      - discovery.type=single-node  
      - xpack.security.enabled=true  
      - xpack.security.http.ssl.enabled=false  
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}  
      - bootstrap.memory_lock=true  
      - ES_JAVA_OPTS=-Xms1g -Xmx1g  
    ports:  
      - "9200:9200"  
    volumes:  
      - es_data:/usr/share/elasticsearch/data  
    networks:  
      - elasticnet  
    restart: unless-stopped  
  
  kibana:  
    image: docker.elastic.co/kibana/kibana:8.15.2  
    container_name: kibana  
    environment:  
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200  
      - ELASTICSEARCH_USERNAME=kibana_system  
      - ELASTICSEARCH_PASSWORD=${KIBANA_PASSWORD}  
      - SERVER_NAME=kibana.local  
      - TELEMETRY_ENABLED=false  
    ports:  
      - "5601:5601"  
    depends_on:  
      - elasticsearch  
      - setup  
    networks:  
      - elasticnet  
    restart: unless-stopped  
  
volumes:  
  es_data:  
  
networks:  
  elasticnet:  
    driver: bridge
```