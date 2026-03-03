# 📥 Gestão Automática do WebDriver

Gerenciar versões do ChromeDriver manualmente pode ser frustrante. Este guia mostra como automatizar o download do driver compatível com sua versão do Chrome.

## 🛠️ Opção 1: Script Customizado (Nativo)

Este script detecta seu sistema operacional e baixa a última versão estável do `chromedriver.storage.googleapis.com`.

```python
import os
import platform
import requests
from selenium import webdriver

def download_webdriver():
    system = platform.system()
    webdriver_dir = os.path.join(os.getcwd(), 'webdriver')
    os.makedirs(webdriver_dir, exist_ok=True)

    # Definição de URLs baseada no OS
    if system == 'Windows':
        driver_name = 'chromedriver_win32.zip'
    elif system == 'Linux':
        driver_name = 'chromedriver_linux64.zip'
    else:
        return None

    # Lógica de download simplificada
    latest_release_url = 'https://chromedriver.storage.googleapis.com/LATEST_RELEASE'
    version = requests.get(latest_release_url).text.strip()
    download_url = f'https://chromedriver.storage.googleapis.com/{version}/{driver_name}'
    
    # ... (lógica de salvar arquivo e extrair zip)
```

## ✨ Opção 2: Webdriver Manager (Recomendado)

Atualmente, a melhor prática é usar a biblioteca `webdriver-manager`, que faz tudo isso em uma única linha:

```bash
pip install webdriver-manager
```

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

# Isso baixa e configura o driver automaticamente na primeira execução
service = Service(ChromeDriverManager().install())
driver = webdriver.Chrome(service=service)
```

## ✅ Vantagens da Automação

1.  **Compatibilidade**: O driver sempre condiz com a versão instalada do Chrome.
2.  **Portabilidade**: O mesmo código funciona em Windows, Linux e Mac sem ajustes manuais.
3.  **CI/CD**: Essencial para rodar testes em pipelines (GitHub Actions, Jenkins).

---
#python #selenium #automation #webdriver #testing
