#docker-compose #redis #Cache #docker #NoSQL 

1. Certifique-se de ter o Docker instalado em sua máquina. Você pode instalar o Docker a partir do site oficial do Docker.

2. Abra o terminal ou prompt de comando e execute o seguinte comando para baixar a imagem Redis do Docker Hub:

```
docker pull redis
```

Este comando baixará a imagem Redis mais recente disponível no Docker Hub.

3. Depois que a imagem for baixada, execute o seguinte comando para iniciar um contêiner Redis:

```
docker run --name my-redis -d redis
```

Este comando criará um novo contêiner chamado "my-redis" e iniciará o servidor Redis em segundo plano.

4. Para se conectar ao servidor Redis, execute o seguinte comando:

```
docker exec -it my-redis redis-cli
```

Este comando conectará você ao cliente Redis dentro do contêiner "my-redis

Para se conectar ao servidor Redis, execute o seguinte comando:

Agora você pode começar a usar o Redis dentro do contêiner Docker. Quando terminar, você pode parar o contêiner Redis usando o seguinte comando:

```
docker stop my-redis
```

E se quiser remover o contêiner, execute o seguinte comando:

```
docker rm my-redis
```

Lembre-se de que a remoção do contêiner também removerá todos os dados armazenados nele. Se você quiser manter seus dados mesmo após a remoção do contêiner, é recomendável usar um volume do Docker para armazenar seus dados fora do contêiner.

## docker-compose
Subindo container com docker compose,

```d
version: "3"  
  
services:  
  redis:  
    image: redis  
    ports:  
      - "6379:6379"  
    volumes:  
      - redis_data:/data  
    
volumes:  
  redis_data:
```