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
  
## Removendo containers  
  
`docker rm <container_id>` - Remove o container  
  
`docker rm $(docker ps -a -q)` - Remove todos os containers

#docker #alura #curso
