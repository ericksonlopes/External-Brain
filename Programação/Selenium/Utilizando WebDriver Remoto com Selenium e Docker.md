#docker #webdriver #selenium #python

Neste tutorial, vamos aprender como utilizar o WebDriver Remoto com o Selenium e Docker para executar testes automatizados em navegadores. O WebDriver Remoto permite que você controle um navegador em uma máquina remota, o que é especialmente útil para testes de integração ou quando você precisa executar testes em diferentes ambientes.

## Pré-requisitos

Certifique-se de ter os seguintes componentes instalados em seu ambiente de desenvolvimento:

- Python: a linguagem de programação usada pelo Selenium.
- Pip: um gerenciador de pacotes para instalar as dependências do Selenium.
- Docker: uma plataforma de contêineres para executar o ambiente de teste remoto.

## Configurando o Ambiente Remoto

Para começar, vamos configurar o ambiente remoto usando o Docker. Siga as etapas abaixo:

1. Instale o Docker em seu sistema. Você pode encontrar instruções de instalação detalhadas no [site oficial do Docker](https://docs.docker.com/get-docker/).

2. Crie um arquivo chamado `docker-compose.yml` no diretório raiz do seu projeto e cole o seguinte conteúdo:

```yaml
version: "3.8"

services:
  selenoid-chrome:
      image: "selenoid/chrome:latest"
      network_mode: bridge
      restart: always
      volumes:
        - "/var/run/docker.sock:/var/run/docker.sock"
      depends_on:
        - selenoid

  selenoid:
    image: "aerokube/selenoid"
    network_mode: bridge
    restart: always
    ports:
      - "4444:4444"
    volumes:
      - "./config:/etc/selenoid/"
      - "/var/run/docker.sock:/var/run/docker.sock"
```

Esse arquivo define dois serviços Docker: `selenoid-chrome` e `selenoid`. Eles fornecerão a infraestrutura necessária para executar os navegadores remotamente.

3. Crie uma pasta chamada `config` no diretório raiz do seu projeto e crie um arquivo chamado `browsers.json` dentro dela. Copie o seguinte conteúdo para esse arquivo:

```json
{
  "chrome": {
    "default": "latest",
    "versions": {
      "latest": {
        "image": "selenoid/chrome:latest",
        "port": "4444",
        "path": "/",
        "env": [
          "TZ=America/Sao_Paulo",
          "LANG=pt_BR.UTF-8",
          "LANGUAGE=pt_BR:en",
          "LC_ALL=pt_BR.UTF-8",
          "NO_PROXY=selenoid,127.0.0.1"
        ]
      },
      "latest_VNC": {
        "image": "selenoid/vnc:chrome_latest",
        "port": "4444",
        "path": "/",
        "env": [
          "TZ=America/Sao_Paulo",
          "LANG=pt_BR.UTF-8",
          "LANGUAGE=pt_BR:en",
          "LC_ALL=pt_BR.UTF-8",
          "NO_PROXY=selenoid,127.0.0.1"
        ]
      }
    }
  }
}
```

4. Com o Docker instalado e as configurações prontas, execute o seguinte comando no terminal ou prompt de comando para iniciar o ambiente remoto:

```
docker-compose up -d
```

Aguarde até que os serviços sejam iniciados. Agora, temos um ambiente Docker pronto para executar testes em navegadores remotamente.

## Escrevendo o Código de Teste

Agora, vamos escrever um código Python para conectar ao WebDriver Remoto e executar um teste em um navegador remoto. Siga os passos abaixo:

1. Abra um editor de código de sua preferência e crie um novo arquivo Python com o nome `remote_test.py`.

2. Cole o seguinte código no arquivo:

```python
from selenium import webdriver
from selenium.webdriver.chrome.webdriver import WebDriver


def get_webdriver() -> WebDriver:
    options = webdriver.ChromeOptions()
    ret_driver = webdriver.Remote(command_executor='http://localhost:4444/wd/hub', options=options)
    return ret_driver


if __name__ == '__main__':
    driver = get_webdriver()

    driver.get('https://www.google.com')
    print('chrome', driver.title)
    driver.quit()
```

3. Certifique-se de ter o Selenium instalado em seu ambiente. Se ainda não o tiver, execute o seguinte comando no terminal ou prompt de comando:

```
pip install selenium
```

4. Agora, execute o arquivo `remote_test.py` no terminal ou prompt de comando:

```
python remote_test.py
```

O código irá se conectar ao WebDriver Remoto e abrirá o Google no navegador remoto. O título da página será impresso no console.

Parabéns! Você aprendeu como utilizar o WebDriver Remoto com o Selenium e Docker para executar testes automatizados em navegadores remotos. Agora você pode explorar ainda mais essa poderosa ferramenta e automatizar seus testes em diferentes ambientes de forma eficiente.

Espero que este tutorial tenha sido útil. Continue aprendendo e aprimorando suas habilidades em Python!
