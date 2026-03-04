# 🔭 Elastic Stack + APM Server — Docker Compose

Docker Compose completo para subir o **Elasticsearch**, **Kibana** e **APM Server** localmente. Ideal para desenvolvimento com observabilidade (traces, métricas e logs centralizados).

## 🏗️ Arquitetura dos Serviços

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  APM Server  │────▶│Elasticsearch │◀────│    Kibana    │
│  :8200       │     │  :9200       │     │  :5601       │
└──────┬───────┘     └──────────────┘     └──────────────┘
       ▲
       │ capture_message() / traces
┌──────┴───────┐
│  Sua App     │
│  (Python)    │
└──────────────┘
```

| Serviço | Porta | Função |
|---|---|---|
| **Elasticsearch** | `9200` | Armazena logs, traces e métricas |
| **Kibana** | `5601` | Dashboard de visualização |
| **APM Server** | `8200` | Recebe dados da aplicação e envia ao Elasticsearch |

## 📄 Docker Compose

```yaml
version: "3.8"

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.12.1
    environment:
      - bootstrap.memory_lock=true
      - cluster.name=docker-cluster
      - cluster.routing.allocation.disk.threshold_enabled=false
      - discovery.type=single-node
      - ES_JAVA_OPTS=-XX:UseAVX=2 -Xms1g -Xmx1g
    ulimits:
      memlock:
        hard: -1
        soft: -1
    volumes:
      - esdata:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - elastic
    healthcheck:
      interval: 20s
      retries: 10
      test: curl -s http://localhost:9200/_cluster/health | grep -vq '"status":"red"'

  kibana:
    image: docker.elastic.co/kibana/kibana:7.12.1
    depends_on:
      elasticsearch:
        condition: service_healthy
    environment:
      ELASTICSEARCH_URL: http://elasticsearch:9200
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
    ports:
      - "5601:5601"
    networks:
      - elastic
    healthcheck:
      interval: 10s
      retries: 20
      test: curl --write-out 'HTTP %{http_code}' --fail --silent --output /dev/null http://localhost:5601/api/status

  apm-server:
    image: docker.elastic.co/apm/apm-server:7.12.1
    depends_on:
      elasticsearch:
        condition: service_healthy
      kibana:
        condition: service_healthy
    cap_add: ["CHOWN", "DAC_OVERRIDE", "SETGID", "SETUID"]
    cap_drop: ["ALL"]
    ports:
      - "8200:8200"
    networks:
      - elastic
    command: >
      apm-server -e
        -E apm-server.rum.enabled=true
        -E setup.kibana.host=kibana:5601
        -E setup.template.settings.index.number_of_replicas=0
        -E apm-server.kibana.enabled=true
        -E apm-server.kibana.host=kibana:5601
        -E output.elasticsearch.hosts=["elasticsearch:9200"]
    healthcheck:
      interval: 10s
      retries: 12
      test: curl --write-out 'HTTP %{http_code}' --fail --silent --output /dev/null http://localhost:8200/

volumes:
  esdata:
    driver: local

networks:
  elastic:
    driver: bridge
```

## 🚀 Subindo o Ambiente

```bash
# Subir todos os serviços
docker-compose up -d

# Verificar se estão saudáveis
docker-compose ps

# Acompanhar logs
docker-compose logs -f apm-server
```

## ✅ Validando os Serviços

```bash
# Elasticsearch
curl http://localhost:9200/_cluster/health?pretty

# APM Server
curl http://localhost:8200/

# Kibana → acessar no browser
# http://localhost:5601
```

## ⚙️ Configurações Relevantes

| Configuração | Descrição |
|---|---|
| `discovery.type=single-node` | Modo standalone (sem cluster) |
| `bootstrap.memory_lock=true` | Evita swap de memória do Elasticsearch |
| `ES_JAVA_OPTS=-Xms1g -Xmx1g` | Heap da JVM fixado em 1 GB |
| `apm-server.rum.enabled=true` | Habilita Real User Monitoring (frontend) |
| `number_of_replicas=0` | Sem réplicas (ambiente de desenvolvimento) |
| `cap_add / cap_drop` | Permissões mínimas para o APM Server |

## 🔗 Conectando sua Aplicação

```python
from elasticapm import Client

apm_client = Client(
    service_name="minha-api",
    server_url="http://localhost:8200",
    environment="development",
)
```

## 📝 Notas

- A ordem de inicialização é garantida por `depends_on` + `condition: service_healthy`.
- Os volumes removidos (`pulsar-data`, `mongodb_data`, `weaviate_data`) e networks (`pulsar-net`) do compose original não são necessários para o stack APM.
- Para o compose do Elasticsearch 8.x com autenticação, veja [[Elasticsearch + kibana docker-compose]].
- Para o client APM em Python, veja [[Elastic APM Client — Singleton Thread-Safe com FastAPI]].
- Para o logger que envia para o APM, veja [[APMLogger — Logger que envia logs para o Elastic APM]].

---
#elasticsearch #kibana #apm #docker #docker-compose #observability
