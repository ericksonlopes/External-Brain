Este guia descreve uma arquitetura avançada de **RAG (Retrieval-Augmented Generation)** que utiliza o **Weaviate** para busca semântica ultrarrápida e o **MongoDB** para recuperar o conteúdo completo dos documentos.

---

## 🏗️ Arquitetura do Pipeline

1.  **Busca Vetorial (Weaviate)**: Recebe a pergunta, transforma em vetor (embedding) e localiza os documentos mais similares (`search_kwargs={"k": 5}`).
2.  **Identificação**: O Weaviate retorna apenas os metadados (ex: `fileName`).
3.  **Recuperação (MongoDB)**: O sistema busca no MongoDB o conteúdo textual completo associado àqueles nomes de arquivos.
4.  **Geração (LLM)**: O conteúdo do Mongo é injetado no prompt do GPT-4 para gerar a resposta final.

---

## 📄 Exemplo de Implementação (LangChain)

```python
import weaviate
from langchain_weaviate import WeaviateVectorStore
from langchain_openai import ChatOpenAI

# 1. Conexão Weaviate Cloud
client = weaviate.connect_to_weaviate_cloud(
    cluster_url=settings.WEAVIATE_URL,
    auth_credentials=AuthApiKey(settings.WEAVIATE_KEY)
)

# 2. Configuração do Vector Store
vectorstore = WeaviateVectorStore(
    client=client,
    index_name="MeuIndex",
    embedding=LCEmbeddingService(model_loader)
)

# 3. Busca com Filtros
retriever = vectorstore.as_retriever(
    search_kwargs={"k": 5, "filters": Filter.by_property("segmento").equal("PV")}
)

# 4. Chain de Resposta (RAG)
llm = ChatOpenAI(model="gpt-4o-mini")
rag_chain = (
    RunnableParallel(context=mongo_retriever, question=RunnablePassthrough())
    | prompt | llm
)
```

## 🎯 Vantagens

- **Escalabilidade**: Weaviate gerencia bilhões de vetores.
- **Consistência**: O MongoDB atua como a "Single Source of Truth" para o conteúdo textual pesado.
- **Filtros Híbridos**: Permite filtrar por propriedades (segmento, data) antes da busca vetorial.

---
#ia #weaviate #mongodb #langchain #rag #embedding #vector-db
