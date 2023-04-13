#python #docker #selenoid

Para conectar o Selenium com o Docker, você precisa primeiro ter certeza de que o container do Selenium está em execução. Você pode fazer isso executando o seguinte comando no terminal:

```bash
docker run -d -p 4444:4444 selenium/standalone-chrome
```

Isso inicia o container do Selenium usando o Chrome como navegador. O comando acima mapeia a porta do host (4444) para a porta do container do Selenium (4444).

Agora, você pode se conectar ao Selenium a partir do Python usando a biblioteca `selenium` e especificando a URL do servidor do Selenium que está sendo executado no container. Aqui está um exemplo básico:

```python
from selenium import webdriver

# URL do servidor do Selenium
selenium_url = 'http://localhost:4444/wd/hub'

# Configuração do driver do Selenium para usar o servidor do Selenium
options = webdriver.ChromeOptions()
options.add_argument('--no-sandbox')
options.add_argument('--disable-dev-shm-usage')
driver = webdriver.Remote(command_executor=selenium_url, options=options)

# Exemplo de uso do driver do Selenium
driver.get('https://www.google.com')
search_box = driver.find_element_by_name('q')
search_box.send_keys('Python Selenium Docker')
search_box.submit()

# Encerra o driver do Selenium
driver.quit()

```
O código acima configura o driver do Selenium para usar o servidor do Selenium em execução no Docker, abre o site do Google, pesquisa por "Python Selenium Docker" e fecha o navegador. Note que as opções `--no-sandbox` e `--disable-dev-shm-usage` são necessárias para evitar problemas de permissões no Docker.