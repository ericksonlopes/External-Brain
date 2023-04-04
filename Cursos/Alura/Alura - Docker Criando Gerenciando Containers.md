#python #dockerfile #flask #docker #docker-compose #alura #Cache #NoSQL #volumes #course 

# Comandos

`docker run hello-world` - Teste de funcionamento do Docker

`docker pull ubuntu` - Baixa a imagem do Ubuntu

`docker run ubuntu echo "Olá mundo"` - Executa o comando echo no container ubuntu

`docker run ubuntu sleep 1d` - Executa o comando sleep no container ubuntu

`docker run -it ubuntu bash` - Executa o comando bash no container ubuntu

`docker run -d dockersamples/static-site` - Executa o container dockersamples/static-site em background

`docker exec -it <container_id> bash` - Abre um terminal no container

`docker port <container_id>` - Lista as portas mapeadas do container

`docker run -d -p 80:80 dockersamples/static-site` - Executa o container dockersamples/static-site em background e  
mapeia a porta 80 do container para a porta 80 do host

## Listando containers

`docker ps` - Lista os containers em execução

`docker ps -a` - Lista todos os containers

`docker container ls` - Lista os containers em execução

`docker container ls -a` - Lista todos os containers

## Iniciar e parar containers

`docker stop <container_id>` - Para o container

`docker start <container_id>` - Inicia o container

`docker restart <container_id>` - Reinicia o container

`docker pause <container_id>` - Pausa o container

`docker pause -t=0 <container_id>` - Pausa o container por um tempo

`docker unpause <container_id>` - Despausa o container

`docker stop $(docker ps -q)` - Para todos os containers

## Removendo containers

`docker rm <container_id>` - Remove o container

`docker rm $(docker ps -a -q)` - Remove todos os containers

## images

`docker images` - Lista as imagens

`docker inespect <image_id>` - Exibe informações sobre a imagem

`docker history <image_id>` - Exibe o histórico de criação da imagem

## Criando uma imagem a partir de um Dockerfile

```dockerfile  
# Cria uma imagem do docker com python 3.8  
FROM python:3.8  
  
# Define o diretório de trabalho  
WORKDIR /app-node  
  
# Copia os arquivo para o diretório de trabalho  
COPY . .  
  
# Define a variável de ambiente FLASK_APP  
ENV FLASK_APP=app.py  
  
# Instala as dependências  
RUN pip install -r requirements.txt  
  
# Expõe o host que ira rodar a aplicação (por padrão a porta é 5000)  
ENTRYPOINT ["flask", "run", "--host=0.0.0.0"]  
```  

``docker build -t erickson/app-python:1.0 .`` - Cria a imagem a partir do Dockerfile, erickson/app-python é o nome da  
imagem e 1.0 é a versão, e o . indica que o Dockerfile está no diretório atual

``docker run -p 5000:5000 erickson/app-python:1.0`` - Executa o container

```dockerfile  
FROM python:3.8  
  
# Define o diretório de trabalho  
WORKDIR /app-node  
  
# Copia os arquivo para o diretório de trabalho  
COPY . .  
  
# Define a variável de ambiente FLASK_APP  
ENV FLASK_APP=app.py  
  
# Define a variável PORT  
ARG PORT=5000  
  
# Define a variável de ambiente PORT  
ENV PORT=$PORT  
  
# Expõe a porta 5000 (indica para outros usuários que a porta 5000 será exposta)  
EXPOSE $PORT  
  
# Instala as dependências  
RUN pip install -r requirements.txt  
  
# Expõe o host que ira rodar a aplicação (por padrão a porta é 5000)  
ENTRYPOINT ["flask", "run", "--host=0.0.0.0"]  
```  

#virtual-environment #

`docker tag <imagem> <novo nome da imagem>` - Adiciona para outro repositório

`docker push <imagem>` - Envia a imagem para o repositório

`docker container rm $(docker container ls -aq)` - Remove todos os containers (-aq lista todos os containers)

`docker rmi $(docker images -aq)` - Remove todas as imagens (-q lista todas as imagens)

`docker rmi $(docker images -aq) --force` - Remove todas as imagens forçadamente

`docker ps -s` - Lista os containers e o tamanho de cada um

## persistencia de dados

- Maneira antiga de criar para dados persistentes

`docker run -it -v /home/erickson/Documentos/Projetos/Python:/app-python erickson/app-python:1.0 bash` - Persistencia
de  
dados sobe novos container com os dados

- maneira nova de criar para dados persistentes

`docker run –mount type=bind,source=/home/diretorio,target=/app nginx` - Persistencia de dados sobe novos container
com  
os dados

## Volume

`docker volume create <nome do volume>` - Cria um volume

`docker volume ls` - Lista os volumes

`docker run -it -v meu-volume:/app ubuntu bash` - Cria um container com o volume criado

- se o volume não existir ele cria

## tmpfs

``docker run -it --tmpfs=/app ubuntu bash`` - Cria um container com o volume criado com pasta temporária

## network

`docker network ls` - Lista as redes

- com o `docker inspect <container_id>` é possível ver as configurações de rede do container

```json  
{
  "Networks": {
    "bridge": {
      "IPAMConfig": null,
      "Links": null,
      "Aliases": null,
      "NetworkID": "4d43933633b8677715093daac6e4379f77097862f7de795e11668fb7b5b98eb2",
      "EndpointID": "21b41f3f84d878d28191bbfa0e8b4ca64a7f288dd9588992a7df40e8fe00e7c1",
      "Gateway": "172.17.0.1",
      "IPAddress": "172.17.0.2",
      "IPPrefixLen": 16,
      "IPv6Gateway": "",
      "GlobalIPv6Address": "",
      "GlobalIPv6PrefixLen": 0,
      "MacAddress": "02:42:ac:11:00:02",
      "DriverOpts": null
    }
  }
}  
```  

`docker network create --driver bridge minha-rede` - Cria uma rede

`docker run -it --name ubuntu1 --network minha-rede ubuntu bash` - Cria um container com a rede criada

`docker run -it --network none ubuntu bash` - isolar containers da rede do host

## criando uma rede que se comunica

`docker network create --driver bridge minha-bridge` - cria uma rede

`docker run -d --network minha-bridge --name meu-mongo mongo:4.4.6` - cria um container com a rede criada

`docker run -d --network minha-bridge --name alurabooks -p 3000:3000 aluradocker/alura-books:1.0` - cria um container  
com a rede criada

#rede #network #mongodb

## docker-compose

#docker-compose #docker #compose

- Definindo o arquivo docker-compose.yml

```yaml  
version: "3.9"
services:
  # nome do serviço 
  mongodb:
    image: mongo:4.4.6
    container_name: meu-mongo
    # nome da rede    
    networks:
      - compose-bridge

alurabooks:
  image: aluradocker/alura-books:1.0
  container_name: alurabooks
  networks:
    - compose-bridge
  ports:
    - 3000:3000
  #   dependencia do container alurabooks    
  depends_on:
    - mongodb
#  nome da rede  
networks:
  compose-bridge:
    driver: bridge  
```  

`docker-compose up` - Inicia os containers

`docker-compose up -d` - Inicia os containers em background

`docker system` prune é um comando do Docker que remove todos os recursos não utilizados, incluindo: