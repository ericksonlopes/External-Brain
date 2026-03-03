# 📦 Bucket Manager Service (Linode / S3)

Este serviço fornece uma interface limpa e padronizada para gerenciar operações de objetos em buckets compatíveis com a API S3 (como AWS S3, Linode Object Storage ou MinIO) utilizando a biblioteca `boto3`.

---

## 🛠️ Pré-requisitos

Instale as dependências necessárias:
```bash
pip install boto3 loguru
```

---

## 📄 Implementação do Serviço

```python
import boto3
from loguru import logger
from config.settings import (
    LINODE_BUCKET_ACCESS_KEY, 
    LINODE_BUCKET_ENDPOINT_URL, 
    LINODE_BUCKET_SECRET_KEY
)

class BucketManagerService:
    def __init__(self, bucket_name):
        self.bucket_name = bucket_name
        self.bucket_client = boto3.client(
            's3',
            endpoint_url=LINODE_BUCKET_ENDPOINT_URL,
            aws_access_key_id=LINODE_BUCKET_ACCESS_KEY,
            aws_secret_access_key=LINODE_BUCKET_SECRET_KEY
        )

    def get_item(self, key):
        """Recupera um objeto do bucket."""
        try:
            return self.bucket_client.get_object(Bucket=self.bucket_name, Key=key)
        except Exception as e:
            logger.error(f"Erro ao obter {key}: {str(e)}")
            raise e

    def create_item(self, key, content):
        """Cria um novo objeto (Upload)."""
        try:
            self.bucket_client.put_object(Body=content, Bucket=self.bucket_name, Key=key)
            logger.info(f"Item '{key}' criado com sucesso.")
        except Exception as e:
            logger.error(f"Erro ao criar {key}: {str(e)}")
            raise e

    def delete_item(self, key):
        """Remove um objeto do bucket."""
        try:
            self.bucket_client.delete_object(Bucket=self.bucket_name, Key=key)
            logger.info(f"Item '{key}' deletado.")
        except Exception as e:
            logger.error(f"Erro ao deletar {key}: {str(e)}")
            raise e
```

## 🚀 Como Usar

```python
# Inicialização
service = BucketManagerService('meu-bucket')

# Upload de texto
service.create_item('config.txt', b'Hello Bucket!')

# Download
obj = service.get_item('config.txt')
print(obj['Body'].read())
```

---
#bucket #s3 #cloud #python #storage #boto3
