Você precisa trocar o volume nomeado por um bind mount apontando para `/minio/data` no host.

No seu compose, ajuste para algo assim:

text

`services:   minio:    image: minio/minio:latest    container_name: minio_server    ports:      - "9000:9000"      - "9001:9001"    environment:      MINIO_ROOT_USER: AiJlbjSPOknv      MINIO_ROOT_PASSWORD: 01CIZi94Di4I!!!!s    volumes:      - /minio/data:/data        # <- pasta do host mapeada para /data no container    command: server /data --console-address ":9001"`

E pode remover a seção `volumes:` do final, já que você não vai mais usar volume nomeado:

text

`# volumes: #   minio_data:`

Antes de subir o stack, cria a pasta no host (na máquina onde o Docker está rodando):

bash

`sudo mkdir -p /minio/data sudo chown 1000:1000 /minio/data  # opcional, se der problema de permissão`