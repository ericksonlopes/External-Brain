# 🌌 Criando Itens no Azure Cosmos DB (NoSQL)

O Azure Cosmos DB é um banco de dados multimodelo distribuído globalmente. Este guia foca na API NoSQL (Documentos) utilizando o SDK oficial para Python.

## 🛠️ Instalação

```bash
pip install azure-cosmos
```

## 📄 Exemplo: Criar um Artigo/Documento

Para criar um item, você precisa se conectar ao serviço, acessar o banco de dados e o container (coleção).

```python
import os
from azure.cosmos import CosmosClient, PartitionKey

# Configurações de Conexão (Pegar no portal Azure)
ENDPOINT = "https://seu-cosmos.documents.azure.com:443/"
KEY = "sua-chave-secreta"

client = CosmosClient(ENDPOINT, KEY)

# 1. Criar/Acessar Banco de Dados
database_name = 'BlogDatabase'
database = client.create_database_if_not_exists(id=database_name)

# 2. Criar/Acessar Container (Partition Key é obrigatória)
container_name = 'Articles'
container = database.create_container_if_not_exists(
    id=container_name, 
    partition_key=PartitionKey(path="/category"),
    offer_throughput=400
)

# 3. Criar o Documento (Artigo)
article_item = {
    "id": "1",
    "category": "Tecnologia",
    "title": "Iniciando com Cosmos DB e Python",
    "content": "Conteúdo detalhado sobre o uso do SDK...",
    "tags": ["NoSQL", "Azure", "Python"]
}

container.create_item(body=article_item)
print("Artigo criado com sucesso!")
```

## 📝 Pontos Importantes

- **Partition Key (`/category`)**: Escolher a chave de partição correta é vital para a performance e custo do Cosmos DB.
- **`id` único**: Todo documento deve ter um campo `id` único dentro da mesma partição.
- **Throughput (RU/s)**: O custo é baseado em Unidades de Requisição (Request Units).

---
#azure #cosmos #nosql #python #cloud #database
