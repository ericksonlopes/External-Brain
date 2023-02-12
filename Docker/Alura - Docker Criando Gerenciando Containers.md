#python #dockerfile #flask #docker #docker-compose  
  
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
  
#virtual-environment #variáveis-de-ambiente  
  
`docker tag <imagem> <novo nome da imagem>` - Adiciona para outro repositório  
  
`docker push <imagem>` - Envia a imagem para o repositório  
  
`docker container rm $(docker container ls -aq)` - Remove todos os containers (-aq lista todos os containers)