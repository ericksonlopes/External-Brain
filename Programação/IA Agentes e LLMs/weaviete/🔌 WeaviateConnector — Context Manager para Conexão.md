
Classe que encapsula a conexão com o Weaviate usando o padrão **context manager** (`with`), garantindo que a conexão seja sempre fechada corretamente.

## 🔧 Dependências

```bash
pip install weaviate-client
```

## 📄 Implementação

```python
import weaviate
from weaviate.classes.init import Auth

from src.config.settings import settings


class WeaviateConnector:
    def __init__(self, cluster_url: str, api_key: str):
        self.cluster_url = cluster_url
        self.api_key = api_key
        self._client = None

    def _create_client(self):
        """Cria conexão local ou cloud com base na env var `settings.ENV`."""
        if "local" in settings.ENV:
            return weaviate.connect_to_local(
                host="localhost",  # ou host.docker.internal dentro de container
                port=8081,
                grpc_port=50051,
            )
        else:
            return weaviate.connect_to_weaviate_cloud(
                cluster_url=self.cluster_url,
                auth_credentials=Auth.api_key(self.api_key),
            )

    def __enter__(self):
        """Context manager entry."""
        self._client = self._create_client()
        return self._client

    def __exit__(self, exc_type, exc_val, exc_tb):
        """Context manager exit — fecha a conexão e loga erros."""
        if self._client is not None:
            try:
                self._client.close()
            except Exception:
                pass
            finally:
                self._client = None
```

## 🚀 Exemplo de Uso

```python
connector = WeaviateConnector(
    cluster_url="https://meu-cluster.weaviate.cloud",
    api_key="minha-api-key"
)

with connector as client:
    collection = client.collections.get("MeuIndex")
    results = collection.query.near_text(query="busca semântica", limit=5)
    print(results)
# Conexão fechada automaticamente ao sair do `with`
```

## 📝 Notas

- A seleção local/cloud é **automática** — baseada em `settings.ENV` (se contém `"local"`, conecta localmente).
- Para Docker-in-Docker, use `host.docker.internal` em vez de `localhost`.
- O `__exit__` garante o `close()` mesmo em caso de exceção.
- Para a arquitetura completa de RAG com Weaviate + MongoDB, veja [[🧠 RAG Híbrido - Weaviate (Busca Vetorial) + MongoDB]].

---
#python #weaviate #vector-db #context-manager #ia 