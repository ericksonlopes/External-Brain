#docker-compose #redis #Cache 

1.  Certifique-se de ter o Docker instalado em sua máquina. Você pode instalar o Docker a partir do site oficial do Docker.
    
2.  Abra o terminal ou prompt de comando e execute o seguinte comando para baixar a imagem Redis do Docker Hub:

```
docker pull redis
```

Este comando baixará a imagem Redis mais recente disponível no Docker Hub.
    
3.  Depois que a imagem for baixada, execute o seguinte comando para iniciar um contêiner Redis:
    

```
docker run --name my-redis -d redis
```
    
Este comando criará um novo contêiner chamado "my-redis" e iniciará o servidor Redis em segundo plano.
    
4.  Para se conectar ao servidor Redis, execute o seguinte comando:
    
perlCopy code

```docker exec -it my-redis redis-cli```

Este comando conectará você ao cliente Redis dentro do contêiner "my-redis

Para se conectar ao servidor Redis, execute o seguinte comando:

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
  
  app:  
    build: .  
    ports:  
      - "8000:8000"  
    depends_on:  
      - redis  
    environment:  
      - REDIS_HOST=redis  
  
volumes:  
  redis_data:
```