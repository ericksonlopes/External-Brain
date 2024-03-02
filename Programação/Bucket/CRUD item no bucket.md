#bucket #linode #aws #s3 #boto3 #python

# Bucket Manager Service

```python
import boto3
from loguru import logger

from config.settings import LINODE_BUCKET_ACCESS_KEY, LINODE_BUCKET_ENDPOINT_URL, LINODE_BUCKET_SECRET_KEY

class BucketManagerService:
    def __init__(self, bucket_name):
        self.bucket_name = bucket_name
        self.bucket_client = boto3.client(
            's3',
            endpoint_url=LINODE_BUCKET_ENDPOINT_URL,
            aws_access_key_id=LINODE_BUCKET_ACCESS_KEY,
            aws_secret_access_key=LINODE_BUCKET_SECRET_KEY
        )

    def get_item(self, url):
        try:
            img = self.bucket_client.get_object(Bucket=self.bucket_name, Key=url)
            return img
        except Exception as e:
            logger.error(f"Falha ao obter o item {url} do bucket {self.bucket_name}: {str(e)}")
            raise e

    def create_item(self, file_name, content):
        try:
            self.bucket_client.put_object(Body=content, Bucket=self.bucket_name, Key=file_name)
            logger.info(f"Item '{file_name}' criado com sucesso no bucket '{self.bucket_name}'.")
        except Exception as e:
            logger.error(f"Falha ao criar o item '{file_name}' no bucket '{self.bucket_name}': {str(e)}")
            raise e

    def delete_item(self, file_name):
        try:
            self.bucket_client.delete_object(Bucket=self.bucket_name, Key=file_name)
            logger.info(f"Item '{file_name}' deletado com sucesso do bucket '{self.bucket_name}'.")
        except Exception as e:
            logger.error(f"Falha ao deletar o item '{file_name}' do bucket '{self.bucket_name}': {str(e)}")
            raise e

    def update_item(self, file_name, new_image_data):
        try:
            self.bucket_client.upload_image(file_name, new_image_data)
            logger.info(f"Imagem {file_name} atualizada com sucesso no bucket {self.bucket_name}.")
        except Exception as e:
            logger.error(f"Falha ao atualizar a imagem {file_name} no bucket {self.bucket_name}: {str(e)}")
            raise e
```

Este é um serviço para gerenciar operações básicas em um bucket do serviço de armazenamento de objetos da Linode usando a biblioteca boto3 do Python.

## Requisitos

Certifique-se de ter instalado os seguintes pacotes:

- `boto3`
- `loguru`
- As configurações necessárias em `bee_news.config.settings`:
  - `LINODE_BUCKET_ACCESS_KEY`
  - `LINODE_BUCKET_ENDPOINT_URL`
  - `LINODE_BUCKET_SECRET_KEY`

## Uso

```python
from bee_news.services.bucket_manager_service import BucketManagerService

# Inicialize o serviço com o nome do bucket desejado
bucket_manager = BucketManagerService('nome_do_bucket')

# Obter um item do bucket
item = bucket_manager.get_item('caminho/para/o/item')

# Criar um novo item no bucket
bucket_manager.create_item('nome_do_arquivo', conteudo_do_arquivo)

# Deletar um item do bucket
bucket_manager.delete_item('nome_do_arquivo')

# Atualizar um item no bucket
bucket_manager.update_item('nome_do_arquivo', novo_conteudo_do_arquivo)
```

## Métodos

### `__init__(self, bucket_name: str)`

Construtor da classe `BucketManagerService`. Inicializa o serviço com o nome do bucket fornecido.

#### Parâmetros

- `bucket_name` (str): O nome do bucket.

### `get_item(self, url: str) -> dict`

Recupera um item do bucket usando sua URL.

#### Parâmetros

- `url` (str): A URL do item no bucket.

#### Retorna

- `dict`: Os dados do item.

### `create_item(self, file_name: str, content: bytes) -> None`

Cria um novo item no bucket com o nome e conteúdo fornecidos.

#### Parâmetros

- `file_name` (str): O nome do arquivo a ser criado no bucket.
- `content` (bytes): O conteúdo do arquivo a ser armazenado no bucket.

### `delete_item(self, file_name: str) -> None`

Exclui um item do bucket com o nome fornecido.

#### Parâmetros

- `file_name` (str): O nome do arquivo a ser excluído do bucket.

### `update_item(self, file_name: str, new_image_data: bytes) -> None`

Atualiza um item existente no bucket com novos dados.

#### Parâmetros

- `file_name` (str): O nome do arquivo a ser atualizado no bucket.
- `new_image_data` (bytes): Os novos dados do arquivo a serem atualizados no bucket.
