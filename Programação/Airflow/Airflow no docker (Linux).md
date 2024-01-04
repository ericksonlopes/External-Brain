# Airflow, Python, Docker, Docker Compose

## Antes de começar

Este guia presume que você está familiarizado com Docker e Docker Compose. Se você nunca trabalhou com essas ferramentas antes, reserve um momento para percorrer o [Docker Quick Start](https://docs.docker.com/get-started/) (especialmente a seção sobre [Docker Compose](https://docs.docker.com/get-started/08_using_compose/)) para se familiarizar com o funcionamento delas.

Siga estas etapas para instalar as ferramentas necessárias, caso ainda não o tenha feito.

1. Instale [o Docker Community Edition (CE)](https://docs.docker.com/engine/installation/) em sua estação de trabalho. Dependendo do seu sistema operacional, pode ser necessário configurar o Docker para usar pelo menos 4,00 GB de memória para garantir o correto funcionamento dos contêineres do Airflow. Consulte a seção Recursos na documentação do [Docker para Windows](https://docs.docker.com/docker-for-windows/#resources) ou [Docker para Mac](https://docs.docker.com/docker-for-mac/#resources) para obter mais informações.

2. Instale [o Docker Compose](https://docs.docker.com/compose/install/) versão 2.14.0 ou posterior em sua estação de trabalho.

## Obtenção do `docker-compose.yaml`

Para implantar o Airflow com Docker Compose, você precisa obter o arquivo [docker-compose.yaml](https://airflow.apache.org/docs/apache-airflow/2.8.0/docker-compose.yaml?_x_tr_sl=auto&_x_tr_tl=en&_x_tr_hl=en-US).

3. Execute o seguinte comando para baixar o arquivo `docker-compose.yaml`:

```shell
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.8.0/docker-compose.yaml'
```

4. Agora, vamos configurar o arquivo `docker-compose.yaml` para o nosso projeto.
Ao abrir o arquivo `docker-compose.yaml`, você verá uma estrutura semelhante a esta:

```yaml
...
x-airflow-common:
&airflow-common
# To add custom dependencies or upgrade provider packages, use your extended image.
# Comment the image line, place your Dockerfile in the directory where you placed the docker-compose.yaml
# and uncomment the "build" line below. Then run `docker-compose build` to build the images.
image: ${AIRFLOW_IMAGE_NAME:-apache/airflow:2.8.0}
# build: .
environment:
&airflow-common-env
...
```

Você precisa comentar a linha `image: ${AIRFLOW_IMAGE_NAME:-apache/airflow:2.8.0}` e descomentar a linha `build: .` para que o Docker Compose possa construir a imagem do Airflow com base no Dockerfile que você criará.

```yaml
...
x-airflow-common:
&airflow-common
# To add custom dependencies or upgrade provider packages, use your extended image.
# Comment the image line, place your Dockerfile in the directory where you placed the docker-compose.yaml
# and uncomment the "build" line below. Then run `docker-compose build` to build the images.
image: ${AIRFLOW_IMAGE_NAME:-extending_airflow:latest}
# build: .
environment:
&airflow-common-env
...
```

5. Crie um Dockerfile para estender o Airflow.

```dockerfile
FROM apache/airflow:2.7.1-python3.9

# Copy the Pipfile and Pipfile.lock to the working directory
COPY Pipfile /Pipfile
COPY Pipfile.lock /Pipfile.lock

# Install pipenv and dependencies from Pipfile
RUN pip install --user --upgrade pip
RUN pip install --upgrade pipenv && pipenv install --system
```

## Executando o Docker

Estrutura da pasta:

```plaintext
meu_projeto/
│
├── Dockerfile
├── docker-compose.yml
├── Pipfile
└── Pipfile.lock
```

### Executando o Docker

1. Crie os diretórios necessários para o Airflow:

```shell
mkdir -p ./dags ./logs ./plugins ./config ./data
```

2. Crie um arquivo `.env` com o UID do usuário atual. Isso é necessário para que o Airflow atribua permissões corretas aos arquivos criados no host. O arquivo `.env` deve estar no mesmo diretório que o arquivo `docker-compose.yaml`. Para criar o arquivo `.env`, execute o seguinte comando:

```shell
echo -e "AIRFLOW_UID=$(id -u)" > .env
```

3. Execute o seguinte comando para construir a imagem do Airflow:

```shell
docker build . --tag extending_airflow:latest
```

4. Execute o seguinte comando para iniciar o Docker Compose:

```shell
docker compose up -d --build
```

- Se você quiser parar o Docker Compose, execute o seguinte comando:

```shell
docker compose down
```

5. Acesse o Airflow em seu navegador em http://localhost:8080.

## Arquivos fora do diretório do projeto

Se você deseja que o Airflow encontre arquivos em um diretório diferente do padrão, pode alterar o volume no `docker-compose.yaml` para que ele encontre os arquivos no diretório desejado em sua máquina. Como no exemplo abaixo:

```yaml
volumes:
   - ${AIRFLOW_PROJ_DIR:-.}/dags:/opt/airflow/dags
   - ${AIRFLOW_PROJ_DIR:-.}/logs:/opt/airflow/logs
   - ${AIRFLOW_PROJ_DIR:-.}/config:/opt/airflow/config
   - ${AIRFLOW_PROJ_DIR:-.}/plugins:/opt/airflow/plugins

   - /home/erickson/my_project/scraping:/opt/airflow/scraping
```

Supondo que dentro do diretório `scraping` você tenha um arquivo chamado `google.py` e dentro deste arquivo haja uma classe chamada `ScrapingGoogle`, você pode chamar o arquivo em sua DAG da seguinte maneira:

```python
from scraping.google import ScrapingGoogle
```