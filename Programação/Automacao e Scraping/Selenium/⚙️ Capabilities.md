# 🌐 Configurando Capabilities no Selenium (Remote)

Este guia explica como configurar o Selenium para rodar em modo **Remote**, geralmente utilizado em ambientes de produção com **Selenium Grid** ou **Docker**.

## ⚙️ Chrome Options e Capabilities

Ao rodar o Selenium em containers, algumas flags são essenciais para evitar travamentos:

- `--headless`: Executa sem abrir a interface gráfica (obrigatório em servidores).
- `--no-sandbox`: Desativa a camada de segurança do sandbox (necessário em alguns ambientes Docker).
- `--disable-dev-shm-usage`: Evita problemas de limite de memória compartilhada em sistemas Linux.
- `--disable-gpu`: Desativa aceleração de hardware (útil para economizar recursos).

## 📄 Implementação do WebDriver Remoto

```python
from selenium import webdriver
from selenium.webdriver.chrome.webdriver import WebDriver
from config import URL_WEBDRIVER # Ex: "http://localhost:4444/wd/hub"

def get_webdriver() -> WebDriver:
    options = webdriver.ChromeOptions()
    
    # Configurando argumentos de performance e ambiente
    options.add_argument('--headless')
    options.add_argument('--no-sandbox')
    options.add_argument('--disable-dev-shm-usage')
    options.add_argument('--disable-gpu')

    # Iniciando a sessão remota
    return webdriver.Remote(
        command_executor=URL_WEBDRIVER, 
        options=options
    )
```

## 📝 Notas de Uso

Utilize o `webdriver.Remote` sempre que o navegador não estiver instalado na mesma máquina onde o script Python está rodando. Isso permite escalar sua automação usando clusters.

---
#python #selenium #devops #automation #docker
