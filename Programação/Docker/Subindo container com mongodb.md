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

#docker-compose 
1. Crie um arquivo chamado `docker-compose.yml` com o seguinte conteúdo:

```yaml
version: '3'

services:
  mongodb:
    image: mongo
#   restart: always
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

Explicando o que cada linha faz:

- **version**: especifica a versão do Docker Compose que estamos usando.
- **services**: especifica os serviços que queremos executar.
- **mongodb**: nome que damos ao nosso serviço MongoDB, pode ser alterado.
- **image**: especifica a imagem do MongoDB que queremos usar. Neste caso, estamos usando a imagem oficial do MongoDB no Docker Hub.
- **restart**: especifica que o serviço deve ser sempre reiniciado em caso de falha.
- **ports**: especifica a porta que queremos expor para o host. Aqui, estamos expondo a porta 27017, que é a porta padrão do MongoDB.

Com este arquivo docker-compose.yml, você pode executar o seguinte comando na linha de comando na pasta onde o arquivo está localizado para iniciar o serviço MongoDB:

```yaml
docker-compose up -d
```

Para verificar se o serviço está rodando, execute o seguinte comando:

```yaml
docker-compose ps
```