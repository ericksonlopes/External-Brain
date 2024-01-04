#airflow #python #docker #docker-compose 

## Antes de você começar

Este procedimento pressupõe familiaridade com Docker e Docker Compose. Se você nunca trabalhou com essas ferramentas antes, reserve um momento para percorrer o [Docker Quick Start](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/get-started/) (especialmente a seção sobre [Docker Compose](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/get-started/08_using_compose/) ) para se familiarizar com como elas funcionam.

Siga estas etapas para instalar as ferramentas necessárias, caso ainda não tenha feito isso.

1. Instale [o Docker Community Edition (CE)](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/engine/installation/) em sua estação de trabalho. Dependendo do seu sistema operacional, pode ser necessário configurar o Docker para usar pelo menos 4,00 GB de memória para que os contêineres do Airflow funcionem corretamente. Consulte a seção Recursos na documentação do [Docker para Windows](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/docker-for-windows/%23resources) ou [Docker para Mac](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/docker-for-mac/%23resources) para obter mais informações.
    
2. Instale [o Docker Compose](https://translate.google.com/website?sl=auto&tl=en&hl=en-US&u=https://docs.docker.com/compose/install/) v2.14.0 ou mais recente em sua estação de trabalho.


## Buscando`docker-compose.yaml`[](https://airflow-apache-org.translate.goog/docs/apache-airflow/stable/howto/docker-compose/index.html?_x_tr_sl=auto&_x_tr_tl=en&_x_tr_hl=en-US#fetching-docker-compose-yaml "Link permanente para este título")

Para implantar o Airflow no Docker Compose, você deve buscar [docker-compose.yaml](https://airflow-apache-org.translate.goog/docs/apache-airflow/2.8.0/docker-compose.yaml?_x_tr_sl=auto&_x_tr_tl=en&_x_tr_hl=en-US).

3. Execute o seguinte comando para buscar o arquivo`docker-compose.yaml`:

```shell
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.8.0/docker-compose.yaml'
```

4. Agora vamos configurar o arquivo `docker-compose.yaml` para o nosso projeto.
ao abrir o arquivo `docker-compose.yaml` você vai ver que ele tem uma estrutura parecida com essa:

```yaml
...
x-airflow-common:
&airflow-common
# In order to add custom dependencies or upgrade provider packages you can use your extended image.
# Comment the image line, place your Dockerfile in the directory where you placed the docker-compose.yaml
# and uncomment the "build" line below, Then run `docker-compose build` to build the images.
image: ${AIRFLOW_IMAGE_NAME:-apache/airflow:2.8.0}
# build: .
environment:
&airflow-common-env
...
```

Você irá comentar a linha `image: ${AIRFLOW_IMAGE_NAME:-apache/airflow:2.8.0}` e descomentar a linha `build: .` para que o docker-compose possa construir a imagem do airflow com base no Dockerfile que você irá criar.

```yaml
...
x-airflow-common:
&airflow-common
# In order to add custom dependencies or upgrade provider packages you can use your extended image.
# Comment the image line, place your Dockerfile in the directory where you placed the docker-compose.yaml
# and uncomment the "build" line below, Then run `docker-compose build` to build the images.
image: ${AIRFLOW_IMAGE_NAME:-extending_airflow:latest}
# build: .
environment:
&airflow-common-env
...
```

Se caso você queria que o ariflow encontre arquivos em um diretório diferente do que o padrão, você pode alterar o volume do docker-compose.yaml para que ele encontre os arquivos no diretório que você deseja dentro de sua maquina.
como no exemplo abaixo:

5. Crie um dockerfile para estender o airflow

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

Estrutura da pasta:

```
meu_projeto/
│
├── Dockerfile
├── docker-compose.yml
├── Pipfile
└── Pipfile.lock
```

## Rodando o docker

1. Crie os diretórios necessários para o Airflow:
```shell
mkdir -p ./dags ./logs ./plugins ./config ./data
```

2. Crie um arquivo .env com o UID do usuário atual. Isso é necessário para que o Airflow possa atribuir permissões corretas aos arquivos criados no host. O arquivo .env deve estar no mesmo diretório que o arquivo docker-compose.yaml. Para criar o arquivo .env, execute o seguinte comando:
```shell
echo -e "AIRFLOW_UID=$(id -u)" > .env
```

3. Execute o seguinte comando para iniciar o Airflow dockerfile:
```shell
docker build . --tag extending_airflow:latest
```

4. Execute o seguinte comando para iniciar o docker-compose:
```shell
docker compose up -d --build
```

Caso você queira parar o docker-compose, execute o seguinte comando:
```shell
docker compose down
```

5. Acesse o Airflow em seu navegador em http://localhost:8080.


## Arquivos fora do diretório do projeto

```yaml
volumes:
    - ${AIRFLOW_PROJ_DIR:-.}/dags:/opt/airflow/dags
    - ${AIRFLOW_PROJ_DIR:-.}/logs:/opt/airflow/logs
    - ${AIRFLOW_PROJ_DIR:-.}/config:/opt/airflow/config
    - ${AIRFLOW_PROJ_DIR:-.}/plugins:/opt/airflow/plugins
    
    - /home/erickson/my_project/scraping:/opt/airflow/scraping
```

Vamos supor que dentro do diretório `scraping` você tenha um arquivo chamado `google.py` e dentro deste arquivo você tenha uma classe chamada `ScrapingGoogle` que você deseja que a dag execute ele, para chamar o arquivo basta chamar da seguinte forma em sua dag.

```python
from scraping.google import ScrapingGoogle
```