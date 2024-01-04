#airflow #python #docker #docker-compose 

## Antes de você começar

Este procedimento pressupõe familiaridade com Docker e Docker Compose. Se você nunca trabalhou com essas ferramentas antes, reserve um momento para percorrer o [Docker Quick Start](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/get-started/) (especialmente a seção sobre [Docker Compose](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/get-started/08_using_compose/) ) para se familiarizar com como elas funcionam.

Siga estas etapas para instalar as ferramentas necessárias, caso ainda não tenha feito isso.

1. Instale [o Docker Community Edition (CE)](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/engine/installation/) em sua estação de trabalho. Dependendo do seu sistema operacional, pode ser necessário configurar o Docker para usar pelo menos 4,00 GB de memória para que os contêineres do Airflow funcionem corretamente. Consulte a seção Recursos na documentação do [Docker para Windows](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/docker-for-windows/%23resources) ou [Docker para Mac](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/docker-for-mac/%23resources) para obter mais informações.
    
2. Instale [o Docker Compose](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/compose/install/) v2.14.0 ou mais recente em sua estação de trabalho.


## Buscando`docker-compose.yaml`[](https://airflow-apache-org.translate.goog/docs/apache-airflow/stable/howto/docker-compose/index.html?_x_tr_sl=auto&_x_tr_tl=en&_x_tr_hl=en-US#fetching-docker-compose-yaml "Link permanente para este título")

Para implantar o Airflow no Docker Compose, você deve buscar [docker-compose.yaml](https://airflow-apache-org.translate.goog/docs/apache-airflow/2.8.0/docker-compose.yaml?_x_tr_sl=auto&_x_tr_tl=en&_x_tr_hl=en-US).

Execute o seguinte comando para buscar o arquivo`docker-compose.yaml`:

```shell
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.8.0/docker-compose.yaml'
```

Crie um dockerfile para estender o airflow

```dockerfile
FROM apache/airflow:2.7.1-python3.9

# Copie o arquivo Pipfile e Pipfile.lock para o diretório de trabalho
COPY Pipfile /Pipfile
COPY Pipfile.lock /Pipfile.lock

# Instale o pipenv e as dependências do Pipfile
RUN pip install --user --upgrade pip
RUN pip install --upgrade pipenv && pipenv install --system
```

## Agora é só rodar o docker com os comandos abaixo


Crie os diretórios necessários para o Airflow:
```shell
mkdir -p ./dags ./logs ./plugins ./config ./data
```

Crie um arquivo .env com o UID do usuário atual. Isso é necessário para que o Airflow possa atribuir permissões corretas aos arquivos criados no host. O arquivo .env deve estar no mesmo diretório que o arquivo docker-compose.yaml. Para criar o arquivo .env, execute o seguinte comando:
```shell
echo -e "AIRFLOW_UID=$(id -u)" > .env
```

Execute o seguinte comando para iniciar o Airflow dockerfile:
```shell
docker build . --tag extending_airflow:latest
```

Execute o seguinte comando para iniciar o docker-compose:
```shell
docker compose up -d --build
```