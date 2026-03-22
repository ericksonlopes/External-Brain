# 🐳 MinIO — Object Storage com Docker Compose

Instância do **MinIO** rodando em container para armazenamento de objetos compatível com S3.

## 📦 Dependências

- Docker & Docker Compose

## ⚙️ Implementação

`yaml
version: '3.8'
services:
  minio:
    image: minio/minio:latest
    container_name: minio
    restart: unless-stopped
    ports:
      - "9000:9000"  # API
      - "9001:9001"  # Console UI
    environment:
      MINIO_ROOT_USER: ADMINMINIOROOT
      MINIO_ROOT_PASSWORD: ZQJoESbQOfA5#6*mUsA
    volumes:
      - /var/docker_volumes/minio/data:/data
    command: server /data --console-address ":9001"
    networks:
      - connect
`

## 📝 Tabela de Configuração

| Variável | Descrição |
|----------|-----------|
| 9001 | Porta do console web para gestão manual de buckets. |
| 9000 | Endpoint S3 para conexão via SDK (boto3, etc). |

## 📌 Notas

- Certifique-se que a rede connect externa já existe: docker network create connect.

---
#docker #infra #s3 #minio #object-storage
