# 🍃 CRUD em MongoDB com Python

Este guia apresenta uma classe utilitária para realizar as operações fundamentais de **C**reate, **R**ead, **U**pdate e **D**elete no MongoDB utilizando a biblioteca `pymongo`.

## 🛠️ Instalação

```bash
pip install pymongo
```

## 📄 Classe MongoDB Manager

A classe abaixo encapsula a conexão e os métodos principais, facilitando o reuso em diferentes partes do projeto.

```python
import pymongo

class MongoDB:
    def __init__(self, url, db_name='tests', collection_name='users'):
        self.client = pymongo.MongoClient(url)
        self.db = self.client[db_name]
        self.collection = self.db[collection_name]

    def create(self, data: dict):
        """Insere um novo documento."""
        result = self.collection.insert_one(data)
        return result.inserted_id

    def read(self, query: dict):
        """Busca documentos com base em um filtro."""
        return list(self.collection.find(query))

    def update(self, query: dict, new_data: dict):
        """Atualiza o primeiro documento que der match com a query."""
        result = self.collection.update_one(query, {"$set": new_data})
        return result.modified_count

    def delete(self, query: dict):
        """Remove o primeiro documento que der match."""
        result = self.collection.delete_one(query)
        return result.deleted_count

    def drop_collection(self):
        """Remove a coleção inteira."""
        self.collection.drop()

    def close(self):
        """Fecha a conexão com o banco."""
        self.client.close()
```

## 🚀 Exemplo de Uso

```python
URL_MONGODB = 'mongodb://localhost:27017/'
db = MongoDB(URL_MONGODB)

# Criar
user_id = db.create({'name': 'John Doe', 'active': True})

# Ler
users = db.read({'active': True})

# Atualizar
db.update({'name': 'John Doe'}, {'active': False})

# Deletar
db.delete({'name': 'John Doe'})

db.close()
```

---
#mongodb #nosql #python #database #backend
