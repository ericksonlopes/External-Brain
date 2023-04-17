#venv #virtual-environment #pipenv 

## Primeiro Passo: Ambiente Virtual

Para iniciar uma venv (ambiente virtual) em Python, você pode seguir os seguintes passos:

1.  Abra o terminal do seu sistema operacional
2.  Navegue até o diretório onde deseja criar a venv usando o comando `cd`
3.  Digite o comando `python -m venv venv` para criar a venv (o último venv se refere ao nome do ambiente virtual)
5.  Ative a venv digitando o comando específico para o seu sistema operacional:
    -   Windows: `.\venv\Scripts\activate.bat`
    -   Linux/MacOS: `source venv/bin/activate`

- O projeto deve ficar da seguinte forma:
```
Project/
|__ venv
```


Dessa forma, você terá iniciado a sua venv em Python. Para desativar a venv, basta digitar o comando `deactivate`.

## Segundo Passo:  Pipenv

O Pipenv é uma ferramenta que combina e simplifica a criação e gestão de ambientes virtuais com a instalação de pacotes Python. Para utilizá-lo, siga os seguintes passos:

1.  Abra o terminal do seu sistema operacional
2.  Navegue até o diretório do seu projeto usando o comando `cd`
3.  Instale o Pipenv digitando o comando `pip install pipenv`
4.  Inicialize o ambiente virtual com o comando `pipenv shell`
5.  Instale os pacotes necessários usando o comando `pipenv install nome_do_pacote`
6.  Quando terminar de usar a venv, saia do ambiente digitando o comando `exit`

O Pipenv também oferece outras funcionalidades úteis, como a criação automática de um arquivo Pipfile que lista todos os pacotes instalados no ambiente virtual e as suas dependências, além de permitir que você instale todas as dependências do seu projeto em apenas um comando (`pipenv install`). Com o uso do Pipenv, fica mais fácil garantir que o seu projeto tenha um ambiente confiável e possui todas as dependências corretas.

- O projeto deve ficar da seguinte forma:
```
Project/
|__ venv
|__ Pipfile
|__ Pipfile.lock
```

## Terceiro passo: Testes

1. Execute os seguintes comandos para instalar o pytest e o pytest-cov:

``` 
pipenv install pytest
pipenv install pytest-cov
```

2. Crie uma pasta para seus testes e coloque os seus arquivos de teste dentro dela (De preferência pasta chamada tests com um arquivo `__init__.py` dentro).

3. Crie um arquivo `pytest.ini` na raiz do seu projeto e defina as configurações do pytest.

4. Adicione o seguinte conteúdo ao arquivo `pytest.ini`:

```
[pytest]

addopts = -v --cov=src --cov-report=term-missing --cov-report=html
```

5. Crie um arquivo `.coveragerc` na raiz do seu projeto e defina as configurações do coverage.
    - Definimos que a pasta coverage irá ficar dentro da pasta tests 

```
[html]
directory = ./tests/coverage
```

- O projeto deve ficar da seguinte forma:
 
``` 
Project/
|__ venv
|__ pytest
	|__ _init__.py_
|__ Pipfile
|__ Pipfile.lock
|__ pytest.ini
|__ .coveragerc
``` 


Quarto passo: Pastas do projeto

Crie uma pasta `src` onde ficará todo o código do seu projeto.
8. Crie uma pasta `config` onde ficará o arquivo de configuração do seu projeto e o arquivo `__init__.py` para que o python reconheça a pasta como um módulo.