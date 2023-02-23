#docker

- Cria uma imagem

```bash
docker build -t nome-da-imagem .
``` 

- Inicia um container

```bash 
docker run --name nome-do-container -d -it nome-da-imagem 
``` 

- Inicia um container com a porta 3000 do host mapeada para a porta 3000 do container

```bash
docker run --name nome-do-container -d -it -p 3000:3000 nome-da-imagem 
``` 

#docker-compose

- Inicia os containers

```bash
docker-compose up
``` 

- Inicia os containers em background

```bash
docker-compose up -d
``` 

- Para os containers

```bash
docker-compose down
``` 

- Lista os containers

```bash
docker-compose ps
``` 

```bash
docker-compose logs
``` 

```bash
docker-compose logs -f
``` 
