# 📦 Weaviate Repository — Padrão Repository com Vector DB

Implementação do padrão **Repository** para o Weaviate. Encapsula criação de schema (collection) e operações de escrita com vetores customizados.

## 🏗️ Arquitetura

```
┌────────────────────┐     ┌─────────────────────┐
│  IBookChunkRepo    │     │  WeaviateConnector   │
│  (interface)       │     │  (context manager)   │
└────────┬───────────┘     └──────────┬───────────┘
         │ implements                 │ uses
┌────────▼───────────┐               │
│ BookChunkRepository│───────────────┘
│  - _ensure_schema  │
│  - save_chunks     │
└────────────────────┘
```

## 🔧 Dependências

```bash
pip install weaviate-client pydantic
```

## 📄 Exemplo Simplificado

### 1. Entity (Pydantic)

```python
from pydantic import BaseModel


class BookChunkEntity(BaseModel):
    file_name: str
    content: str
    pages: list[int]
    segment: str
    vector: list[float] | None = None
```

### 2. Repository

```python
from weaviate.classes.config import Configure, Property, DataType, VectorDistances, Tokenization
from weaviate.exceptions import WeaviateBaseError


class BookChunkRepository:
    def __init__(self, connector, collection_name: str = "BookChunks"):
        self.connector = connector
        self.collection_name = collection_name
        self._ensure_schema()

    def _ensure_schema(self):
        """Cria a collection no Weaviate se não existir."""
        properties = [
            Property(name="file_name", data_type=DataType.TEXT),
            Property(name="content", data_type=DataType.TEXT),
            Property(name="pages", data_type=DataType.INT_ARRAY),
            Property(name="segment", data_type=DataType.TEXT, tokenization=Tokenization.FIELD),
        ]

        with self.connector as client:
            try:
                client.collections.get(self.collection_name)
            except WeaviateBaseError:
                client.collections.create(
                    name=self.collection_name,
                    vector_config=Configure.Vectors.self_provided(
                        vector_index_config=Configure.VectorIndex.hnsw(
                            distance_metric=VectorDistances.COSINE,
                        ),
                    ),
                    properties=properties,
                )

    def save_chunks(self, chunks: list[BookChunkEntity]) -> list[str]:
        """Salva uma lista de chunks com seus vetores no Weaviate."""
        if not chunks:
            return []

        with self.connector as client:
            collection = client.collections.get(self.collection_name)
            uuids = []

            with collection.batch.dynamic() as batch:
                for chunk in chunks:
                    uuid = batch.add_object(
                        properties=chunk.model_dump(exclude={"vector"}),
                        vector=chunk.vector,
                    )
                    uuids.append(str(uuid))

            return uuids
```

### 3. Uso

```python
from src.infrastructure.repository.weaviate.weaviate_connector import WeaviateConnector

connector = WeaviateConnector(cluster_url="...", api_key="...")
repo = BookChunkRepository(connector)

chunks = [
    BookChunkEntity(
        file_name="livro.pdf",
        content="Capítulo 1: Introdução à programação...",
        pages=[1, 2, 3],
        segment="PV",
        vector=[0.1, 0.2, 0.3, ...],  # embedding gerado previamente
    ),
]

uuids = repo.save_chunks(chunks)
print(f"Salvos {len(uuids)} chunks")
```

## ⚙️ Conceitos Principais

| Conceito | Descrição |
|---|---|
| `_ensure_schema()` | Garante que a collection existe no `__init__` — idempotente |
| `self_provided` | Vetores são fornecidos pela aplicação (não gerados pelo Weaviate) |
| `HNSW` | Algoritmo de indexação para busca aproximada de vizinhos mais próximos |
| `COSINE` | Métrica de distância — ideal para embeddings normalizados |
| `Tokenization.FIELD` | Campo indexado como valor exato (sem tokenização por palavras) |
| `batch.dynamic()` | Batch adaptativo — ajusta tamanho automaticamente para performance |

## 🔧 Configurações Avançadas (HNSW)

Para produção com grandes volumes de dados:

```python
Configure.VectorIndex.hnsw(
    distance_metric=VectorDistances.COSINE,
    ef_construction=128,    # Qualidade do índice (maior = mais preciso, mais lento)
    max_connections=32,     # Conexões por nó no grafo
    quantizer=Configure.VectorIndex.Quantizer.rq(bits=8),  # Compressão de vetores
)
```

## 📝 Notas

- O `_ensure_schema` usa try/except em vez de `exists()` — mais robusto com a API do Weaviate.
- Os vetores devem ser gerados antes de chamar `save_chunks` (ex: via [[Embeddings com Sentence Transformers e E5]]).
- O connector é injetado via construtor (DI) — veja [[🔌 WeaviateConnector — Context Manager para Conexão]].

---
#python #weaviate #vector-db #repository-pattern #clean-architecture
