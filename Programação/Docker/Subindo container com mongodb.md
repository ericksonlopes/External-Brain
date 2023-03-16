#mongodb #NoSQL #docker

1. Instale o Docker no seu sistema, se ainda não o fez.
2. Abra um terminal e execute o seguinte comando para baixar a imagem do MongoDB:

```yaml
docker pull mongo
```

3. Execute o seguinte comando para criar um novo container do MongoDB:

```yaml
docker run --name mongodb -p 27017:27017 -d mongo
```

4. Execute o seguinte comando para verificar se o container está rodando:

```yaml
docker ps
```

5. Execute o seguinte comando para acessar o container:

```yaml
docker exec -it mongodb bash
```

## Docker Compose

1. Crie um arquivo chamado `docker-compose.yml` com o seguinte conteúdo:

```yaml
version: '3'

services:
  mongodb:
    image: mongo
    ports:
      - '27017:27017'
    volumes:
      - mongodb_data:/data/db
    environment:
      - MONGO_INITDB_ROOT_USERNAME=root
      - MONGO_INITDB_ROOT_PASSWORD=example

volumes:
  mongodb_data:

```
