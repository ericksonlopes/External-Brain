Forma rápida de rodar o Selenium em um container Docker usando a imagem oficial `selenium/standalone-chrome`. Ideal para testes locais sem precisar instalar o Chrome ou o ChromeDriver na máquina.

## 🚀 Subindo o Container

Execute o comando abaixo para iniciar uma instância do Selenium com Chrome:

```bash
docker run -d -p 4444:4444 selenium/standalone-chrome
```

Isso expõe o WebDriver remoto na porta `4444`, pronto para receber conexões.

## 📄 Conectando via Python

```python
from selenium import webdriver
from selenium.webdriver.chrome.webdriver import WebDriver

def get_webdriver() -> WebDriver:
    options = webdriver.ChromeOptions()
    options.add_argument('--no-sandbox')
    options.add_argument('--disable-dev-shm-usage')

    return webdriver.Remote(
        command_executor='http://localhost:4444/wd/hub',
        options=options
    )

if __name__ == '__main__':
    driver = get_webdriver()
    driver.get('https://www.google.com')
    print('chrome', driver.title)
    driver.quit()
```

## ✅ Vantagens

1. **Isolamento**: O navegador roda dentro do container, sem poluir o ambiente local.
2. **Simplicidade**: Um único comando sobe todo o ambiente necessário.
3. **Reprodutibilidade**: Mesma versão do Chrome em qualquer máquina.

## 📝 Notas

- Para configurações mais avançadas (Selenoid, VNC, múltiplos browsers), veja [[🌐 Utilizando WebDriver Remoto com Selenium e Docker]].
- Para entender as flags do Chrome (`--no-sandbox`, etc.), veja [[⚙️ Capabilities]].

---
#python #selenium #docker #automation #webdriver