# 📦 MinIO: Object Storage Local (S3 Compatible)

O **MinIO** é um servidor de armazenamento de objetos de alta performance e código aberto, compatível com a API do Amazon S3. Ideal para testar implementações de **Buckets** localmente.

---

## 🏗️ Configuração via Docker Compose

```yaml
version: '3.8'

services:
  minio:
    image: minio/minio:latest
    container_name: minio_server
    ports:
      - "9000:9000" # Porta da API
      - "9001:9001" # Porta do Console (Web UI)
    environment:
      MINIO_ROOT_USER: admin
      MINIO_ROOT_PASSWORD: admin_password123
    volumes:
      - minio_data:/data
    command: server /data --console-address ":9001"

volumes:
  minio_data:
```

---

## 🌐 Acesso e Console

- **Web UI**: Acesse `http://localhost:9001` no navegador.
- **Login**: Use as credenciais definidas no `MINIO_ROOT_USER` e `PASSWORD`.
- **API**: Para configurar no seu código Python (boto3), use o endpoint `http://localhost:9000`.

## 🐍 Integração Python (boto3)

```python
import boto3

s3 = boto3.client(
    's3',
    endpoint_url='http://localhost:9000',
    aws_access_key_id='admin',
    aws_secret_access_key='admin_password123'
)
```

---
#docker #minio #s3 #storage #bucket #python #devops
