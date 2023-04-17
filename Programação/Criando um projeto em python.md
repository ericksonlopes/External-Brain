#venv #virtual-environment #pipenv #tests #pytest #github #github-actions #workflow

## Primeiro Passo: Ambiente Virtual

Para iniciar uma venv (ambiente virtual) em Python, você pode seguir os seguintes passos:

1.  Abra o terminal do seu sistema operacional
2.  Navegue até o diretório onde deseja criar a venv usando o comando `cd`
3.  Digite o comando `python -m venv venv` para criar a venv (o último venv se refere ao nome do ambiente virtual)
4.  Ative a venv digitando o comando específico para o seu sistema operacional:
    -   Windows: ``.\venv\Scripts\activate.bat``
    -   Linux/MacOS: ``source venv/bin/activate``

- O projeto deve ficar da seguinte forma:
```
Project/
|__ venv
    |__ ...
```

Dessa forma, você terá iniciado a sua venv em Python. Para desativar a venv, basta digitar o comando `deactivate`.

## Segundo Passo: Pipenv

O Pipenv é uma ferramenta que combina e simplifica a criação e gestão de ambientes virtuais com a instalação de pacotes Python. Para utilizá-lo, siga os seguintes passos:

1.  Abra o terminal do seu sistema operacional
2.  Navegue até o diretório do seu projeto usando o comando `cd`
3.  Instale o Pipenv digitando o comando `pip install pipenv`
4.  Inicialize o ambiente virtual com o comando `pipenv shell`
5.  Instale os pacotes necessários usando o comando `pipenv install`
6.  Quando terminar de usar a venv, saia do ambiente digitando o comando `exit`

O Pipenv também oferece outras funcionalidades úteis, como a criação automática de um arquivo Pipfile que lista todos os pacotes instalados no ambiente virtual e as suas dependências, além de permitir que você instale todas as dependências do seu projeto em apenas um comando (`pipenv install`). Com o uso do Pipenv, fica mais fácil garantir que o seu projeto tenha um ambiente confiável e possui todas as dependências corretas.

- O projeto deve ficar da seguinte forma:
```
Project/
|__ venv
    |__ ...
|__ Pipfile
|__ Pipfile.lock
```

## Terceiro passo: Pasta do projeto e pasta de configuração

1. Crie uma pasta `src` onde ficará todo o código do seu projeto e o arquivo `__init__.py` para que o python reconheça a pasta como um módulo
   - Exemplo: arquivos de rotas, arquivos de models, arquivos de controllers, etc.
   - Crie um arquivo `main.py` na pasta `src` e adicione o seguinte código. Usaremos este arquivo para testar se o projeto está funcionando corretamente. Adicione o seguinte código:

```python
def main():
    print("Hello World!")
```

2. Crie uma pasta `config` onde ficará todos os arquivos de configuração do seu projeto. Dentro desta pasta, crie um arquivo `__init__.py` para que o python reconheça a pasta como um módulo.
   - Exemplo: arquivos de configuração do banco de dados, arquivos de configuração do pytest, etc.



O projeto deve ficar da seguinte forma:

```
Project/
|__ venv
    |__ ...
|__ src
    |__ __init__.py
    |__ main.py
|__ config
    |__ __init__.py
|__ Pipfile
|__ Pipfile.lock
```

## Quarto passo: Testes

1. Execute os seguintes comandos para instalar o pytest e o pytest-cov:

```
pipenv install pytest
pipenv install pytest-cov
```

2. Crie uma pasta `tests` para armazenar os testes do seu projeto. Dentro desta pasta, crie um arquivo `__init__.py` para que o python reconheça a pasta como um módulo.
3. Crie um arquivo `pytest.ini` na raiz do seu projeto e defina as configurações do pytest.

4. Adicione o seguinte conteúdo ao arquivo `pytest.ini`:

```
[pytest]
addopts = -v --cov=src --cov-report=term-missing --cov-report=html
```

- ``-v``: mostra os testes que estão a ser executados
- ``--cov=src``: define a pasta que será testada
- ``--cov-report=term-missing``: mostra o coverage no terminal
- ``--cov-report=html``: gera um arquivo html com o coverage

5.   Crie um arquivo `.coveragerc` na raiz do seu projeto e defina as configurações do coverage.
    - directory: define o diretório onde o coverage será gerado

```
[html]
directory = ./tests/coverage
```

6. Crie um arquivo  `test_init.py` para testes dentro do diretório `test` e adicione o código abaixo:

```
def test_init():
    assert True
```

7. Execute o comando `pytest` para rodar os testes e verifique se o coverage foi gerado.

- O projeto deve ficar da seguinte forma:
 
``` 
Project/
|__ venv
|__ src
|__ config
    |__ _init__.py_
|__ Pipfile
|__ Pipfile.lock
|__ pytest.ini
|__ .coveragerc
```

## Quinto Passo: Docker

Para começar, você precisará ter o Docker instalado na sua máquina. Depois, você pode criar um Dockerfile para sua aplicação Python. O Dockerfile é um arquivo de texto que contém as instruções para construir uma imagem do Docker, que é uma espécie de pacote que contém todo o ambiente necessário para executar a sua aplicação.

Um exemplo de Dockerfile para uma aplicação Python simples seria:

```dockerfile
FROM python:3.9  

WORKDIR /app  

COPY . .

RUN pip install --upgrade pipenv && pipenv install --system  

RUN pytest

CMD [ "python", "./run.py" ]
```

- ``FROM``: define a imagem base do Docker que será usada para criar a imagem do seu projeto. Neste caso, estamos usando a imagem oficial do Python 3.9
- ``WORKDIR``: define o diretório de trabalho do contêiner Docker. Todos os comandos subsequentes serão executados neste diretório.
- ``COPY``: copia arquivos do seu sistema local para o contêiner Docker.
- ``RUN``: executa um comando no contêiner Docker.
- ``CMD``: define o comando padrão a ser executado quando o contêiner é iniciado.

Este Dockerfile começa com uma imagem base do Python 3.9 e define um diretório de trabalho chamado `/app`. Em seguida, copia o arquivo `requirements.txt` para o diretório de trabalho e executa o comando `pip install` para instalar as dependências listadas no arquivo.

Depois, ele copia todos os arquivos do diretório atual para o diretório de trabalho no contêiner Docker. Por fim, ele define o comando padrão a ser executado quando o contêiner é iniciado, que é executar o arquivo `app.py`.

- O projeto deve ficar da seguinte forma:
    
```
Project/
|__ venv
|__ src
|__ config
    |__ _init__.py_
|__ Pipfile
|__ Pipfile.lock
|__ pytest.ini
|__ .coveragerc
|__ Dockerfile
```

# Sexto Passo: GitHub Workflow para Tests

Para adicionar um workflow de testes no Github Actions para um projeto Python, você precisará criar um arquivo de configuração `.yml` na pasta `.github/workflows/` do seu repositório. Aqui está um exemplo básico de como fazer isso:

1. Crie uma pasta `.github/workflows/` na raiz do seu repositório.
2. Crie um arquivo `python-test.yml` nessa pasta.
3. Adicione o seguinte código ao arquivo:

```yaml
name: Python Test

on: [ push, pull_request ]

jobs:
   build:

      runs-on: ubuntu-latest

      strategy:
         matrix:
            python-version: [ 3.7, 3.8, 3.9 ]

      steps:
         - uses: actions/checkout@v2

         - name: Set up Python ${{ matrix.python-version }}
           uses: actions/setup-python@v2
           with:
              python-version: ${{ matrix.python-version }}

         - name: Install dependencies
           run: |
              python -m pip install --upgrade pip
              pip install pytest

         - name: Run tests
           run: |
              pytest
```

- ``on``: define quando o workflow será executado
- ``jobs``: define o job que será executado
- ``name``: define o nome do job
- ``runs-on``: define o sistema operacional que será usado
- ``python-version``: define a versão do python que será usada
- ``steps``: define os passos que serão executados
- ``run``: define o comando que será executado

Esse arquivo configura um workflow que será executado quando ocorrerem pushs ou pull requests no repositório. O nome do workflow é "Python Test".

Em seguida, é definido um job chamado "build", que será executado na última versão do Ubuntu. Ele usa a estratégia `matrix` para definir uma lista de versões do Python que o job deve ser executado.

Dentro do job, o primeiro passo é usar a ação `actions/checkout` para clonar o repositório.

Depois, é definido um passo para configurar a versão do Python. Ele usa a ação `actions/setup-python` para instalar a versão especificada do Python.

Em seguida, há um passo para instalar as dependências necessárias, que neste exemplo é apenas o pytest.

Por fim, há um passo para executar os testes usando o pytest.

Com isso, você terá um workflow de testes básico configurado para o seu projeto Python. Claro, você pode personalizar o arquivo `.yml` para incluir outras ações, passos de teste ou configurações específicas para sua aplicação.

obs: Para mais informações sobre o Github Actions, acesse a documentação oficial: https://docs.github.com/pt/actions

- O projeto deve ficar da seguinte forma:
    
```
Project/
|__ venv
|__ src
|__ config
    |__ _init__.py_
|__ .github
    |__ workflows
        |__ python-test.yml
|__ Pipfile
|__ Pipfile.lock
|__ pytest.ini
|__ .coveragerc
|__ Dockerfile
```


# Sétimo Passo: Ignorando arquivos

Para ignorar arquivos no Git, você pode criar um arquivo chamado `.gitignore` na raiz do seu repositório. Esse arquivo deve conter uma lista de arquivos e diretórios que você deseja ignorar. Aqui está um exemplo de como fazer isso:


# Oitavo Passo: Rodando e Testando aplicação

Para construir a imagem Docker, você pode executar o seguinte comando no diretório onde está o Dockerfile:


```bash
docker build -t nome-da-imagem .
```

Isso irá construir a imagem e dar-lhe o nome `nome-da-imagem`. O ponto no final do comando indica que o contexto de construção é o diretório atual.

Para executar o contêiner a partir da imagem, você pode executar o seguinte comando:

```bash
docker run -it --rm nome-da-imagem
```

Isso irá iniciar o contêiner e executar o comando padrão especificado no Dockerfile, que é executar o arquivo `app.py`. O parâmetro `-it` é usado para iniciar o contêiner em modo interativo e a opção `--rm` é usada para remover o contêiner quando ele é interrompido.

Com esses passos, você pode criar e executar uma imagem Docker para sua aplicação Python. Claro, isso é apenas um exemplo simples e você pode personalizar o Dockerfile para incluir outras dependências ou configurações específicas para sua aplicação.


# Conclusão

Neste tutorial, você aprendeu como configurar um projeto Python do zero, incluindo a criação de um ambiente virtual, instalação de dependências, configuração de testes, dockerização e criação de um workflow de testes no Github Actions.

Espero que você tenha gostado do tutorial e que ele tenha sido útil para você. Se você tiver alguma dúvida ou sugestão, deixe um comentário abaixo.
