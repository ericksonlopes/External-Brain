#python #docker #chrome #webdriver #selenium

Para conectar o Selenium com o Docker, você precisa primeiro ter certeza de que o container do Selenium está em execução. Você pode fazer isso executando o seguinte comando no terminal:

```bash
docker run -d -p 4444:4444 selenium/standalone-chrome
```

Isso inicia o container do Selenium usando o Chrome como navegador. O comando acima mapeia a porta do host (4444) para a porta do container do Selenium (4444).

Agora, você pode se conectar ao Selenium a partir do Python usando a biblioteca `selenium` e especificando a URL do servidor do Selenium que está sendo executado no container. Aqui está um exemplo básico:

```python
from selenium import webdriver  
from selenium.webdriver.chrome.webdriver import WebDriver  
  
  
def get_webdriver() -> WebDriver:  
options = webdriver.ChromeOptions()  
options.add_argument('--no-sandbox')
options.add_argument('--disable-dev-shm-usage')
ret_driver = webdriver.Remote(command_executor='http://localhost:4444/wd/hub', options=options)  
return ret_driver  
  
  
if __name__ == '__main__':  
	driver = get_webdriver()  
	  
	driver.get('https://www.google.com')  
	print('chrome', driver.title)  
	driver.quit()
```
O código acima configura o driver do Selenium para usar o servidor do Selenium em execução no Docker, abre o site do Google, pesquisa por "Python Selenium Docker" e fecha o navegador. Note que as opções `--no-sandbox` e `--disable-dev-shm-usage` são necessárias para evitar problemas de permissões no Docker.