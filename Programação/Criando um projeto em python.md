#venv #virtual-environment #pipenv #tests #pytest #github #github-actions #workflow #docker #coverage 

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

## Terceiro passo: Estrutura do projeto

1. Crie uma pasta `src` onde ficará todo o código do seu projeto e o arquivo `__init__.py` para que o python reconheça a pasta como um módulo
   - Exemplo: arquivos de rotas, arquivos de models, arquivos de controllers, etc.
   - Crie um arquivo `main.py` na pasta `src` e adicione o seguinte código. Usaremos este arquivo para testar se o projeto está funcionando corretamente. Adicione o seguinte código:

```python
def main():
    return "Hello World!"
```

2. Crie uma pasta `config` onde ficará todos os arquivos de configuração do seu projeto. Dentro desta pasta, crie um arquivo `__init__.py` para que o python reconheça a pasta como um módulo.
   - Exemplo: arquivos de configuração do banco de dados, arquivos de configuração do pytest, etc.

3. Na raiz do seu projeto, crie um arquivo `run.py` e adicione o seguinte código:
   - Este arquivo será responsável por executar o projeto.

```python
from src.main import main

phase = main()

print(phase)
```

O projeto deve ficar da seguinte forma:

```
Project/
|__ venv
    ...
|__ src
    |__ __init__.py
    |__ main.py
|__ config
    |__ __init__.py
|__ Pipfile
|__ Pipfile.lock
|__ run.py
```

## Quarto passo: Testes

1. Execute os seguintes comandos para instalar o pytest e o pytest-cov:

```
pipenv install pytest
pipenv install pytest-cov
```
- O pytest é uma ferramenta para testes em Python e o pytest-cov é uma extensão do pytest que gera um coverage report.

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

6. Crie um arquivo  `test_main.py` para testes dentro do diretório `test` e adicione o código abaixo:

```
from src.main import main

def test_main():
    assert main() == "Hello World!"
```

7. Execute o comando `pytest` para rodar os testes e verifique se o coverage foi gerado.

- O projeto deve ficar da seguinte forma:
 
``` 
Project/
|__ venv
    |__ ...
|__ src
    |__ __init__.py
    |__ main.py
|__ config
    |__ __init__.py
|__ tests
    |__ __init__.py
    |__ test_main.py
    |__ coverage
        |__ index.html
        ... 
|__ Pipfile
|__ Pipfile.lock
|__ pytest.ini
|__ .coveragerc
|__ run.py
```

## Quinto Passo: Docker

1.  Para começar, você precisará ter o Docker instalado na sua máquina. Depois, você pode criar um ``dockerfile`` para sua aplicação Python. O Dockerfile é um arquivo de texto que contém as instruções para construir uma imagem do Docker, que é uma espécie de pacote que contém todo o ambiente necessário para executar a sua aplicação.

Um exemplo de Dockerfile para uma aplicação Python simples seria:

```dockerfile
FROM python:3.10  

WORKDIR /project

COPY . .

RUN pip install --upgrade pipenv && pipenv install --system  

RUN pytest

CMD [ "python", "./run.py" ]
```

- ``FROM``: define a imagem base do Docker que será usada para criar a imagem do seu projeto. Neste caso, estamos a usar a imagem oficial do Python 3.9
- ``WORKDIR``: define o diretório de trabalho do contêiner Docker. Todos os comandos subsequentes serão executados neste diretório.
- ``COPY``: copia arquivos do seu sistema local para o contêiner Docker.
- ``RUN``: executa um comando no contêiner Docker.
- ``CMD``: define o comando padrão a ser executado quando o contêiner é iniciado.

Este Dockerfile começa com uma imagem base do Python 3.9 e define um diretório de trabalho chamado `/project`. Em seguida, ele copia todos os arquivos do diretório atual para o diretório de trabalho no contêiner Docker. Em seguida, ele instala o pipenv e instala todas as dependências do projeto.

Depois, ele executa o comando `pytest` para executar os testes e, por fim, define o comando padrão para executar o arquivo `run.py` quando o contêiner for iniciado.

2. Para construir a imagem do Docker, execute o seguinte comando na raiz do seu projeto:

```
docker build -t python-project .
```

- ``-t``: define o nome da imagem
- ``python-project``: nome da imagem
- ``.``: define o diretório onde o Dockerfile está localizado(neste caso, na raiz do projeto)

- O projeto deve ficar da seguinte forma:
    
```
Project/
|__ venv
    |__ ...
|__ src
    |__ __init__.py
    |__ main.py
|__ config
    |__ __init__.py
|__ tests
    |__ __init__.py
    |__ test_main.py
    |__ coverage
        |__ index.html
        ... 
|__ Pipfile
|__ Pipfile.lock
|__ pytest.ini
|__ .coveragerc
|__ dockerfile
|__ run.py
```

# Sexto Passo: GitHub Actions (Workflow para testes)

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
            python-version: [ 3.10 ]

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
    |__ ...
|__ .github
    |__ workflows
        |__ python-test.yml
|__ src
    |__ __init__.py
    |__ main.py
|__ config
    |__ __init__.py
|__ tests
    |__ __init__.py
    |__ test_main.py
    |__ coverage
        |__ index.html
        ... 
|__ Pipfile
|__ Pipfile.lock
|__ pytest.ini
|__ .coveragerc
|__ dockerfile
|__ run.py
```

# Sétimo Passo: Ignorando arquivos

Agora que você já configurou o seu projeto Python, é hora de adicionar alguns arquivos e pastas ao arquivo `.gitignore` para ignorar arquivos desnecessários no repositório e ao arquivo `.dockerignore` para ignorar arquivos desnecessários na imagem do Docker.

1. Crie um arquivo `.gitignore` na raiz do seu projeto.
2. Adicione os seguintes arquivos ao arquivo `.gitignore`:

```ignorelang
# venv
venv/

# .pytest_cache
.pytest_cache/

# coverage
test/coverage/
```

4. Crie um arquivo `.dockerignore` na raiz do seu projeto.
5. Adicione os seguintes arquivos ao arquivo `.dockerignore`:


```ignorelang
tests/coverage
.github/workflows/python-test.yml
venv/
```

# Conclusão

Neste tutorial, você aprendeu como configurar um projeto Python do zero, incluindo a criação de um ambiente virtual, instalação de dependências, configuração de testes e criação de uma imagem Docker.

Você também aprendeu como adicionar um workflow de testes no Github Actions para um projeto Python.

Espero que você tenha gostado do tutorial.
