# 🔌 WeaviateConnector — Context Manager para Conexão

Classe que encapsula a conexão com o Weaviate usando o padrão **context manager** (`with`), garantindo que a conexão seja sempre fechada corretamente.

## 🔧 Dependências

```bash
pip install weaviate-client
```

## 📄 Implementação

```python
import weaviate
from weaviate.classes.init import Auth


class WeaviateConnector:
    def __init__(self, cluster_url: str, api_key: str):
        self.cluster_url = cluster_url
        self.api_key = api_key
        self._client = None

    def _create_client(self):
        """Cria a conexão com o Weaviate (local ou cloud)."""
        # Conexão local (Docker)
        return weaviate.connect_to_local(
            host="localhost",
            port=8080,
            grpc_port=50051,
        )

        # Conexão cloud (descomentar para usar)
        # return weaviate.connect_to_weaviate_cloud(
        #     cluster_url=self.cluster_url,
        #     auth_credentials=Auth.api_key(self.api_key),
        # )

    def __enter__(self):
        """Context manager entry."""
        self._client = self._create_client()
        return self._client

    def __exit__(self, exc_type, exc_val, exc_tb):
        """Context manager exit — fecha a conexão e loga erros."""
        if self._client is not None:
            try:
                self._client.close()
            finally:
                self._client = None

        if exc_type is not None:
            print(f"Error during Weaviate operation: {exc_val}")
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

- O `__exit__` garante o `close()` mesmo em caso de exceção.
- Para alternar entre local e cloud, basta trocar o método em `_create_client`.
- Para a arquitetura completa de RAG com Weaviate + MongoDB, veja [[start]].

---
#python #weaviate #vector-db #context-manager #ia