import os  
import platform  
  
import requests  
  
  
# ATUALIAR = https://googlechromelabs.github.io/chrome-for-testing/#stable  
  
def download_webdriver():  
    system = platform.system()  
    webdriver_dir = os.path.join(os.getcwd(), 'webdriver')  
  
    if system == 'Windows':  
        driver_url = 'https://chromedriver.storage.googleapis.com/LATEST_RELEASE'  
        driver_name = 'chromedriver_win32.zip'  
    elif system == 'Linux':  
        driver_url = 'https://chromedriver.storage.googleapis.com/LATEST_RELEASE'  
        driver_name = 'chromedriver_linux64.zip'  
    else:  
        print(f'Não há suporte para o sistema operacional {system}.')  
        return  
  
    os.makedirs(webdriver_dir, exist_ok=True)  
  
    print("*", file=open(os.path.join(webdriver_dir, ".gitignore"), 'w', encoding='utf-8'))  
  
    webdriver_path = os.path.join(webdriver_dir, driver_name)  
  
    if not os.path.exists(webdriver_path):  
        try:  
  
            response = requests.get(driver_url)  
            version = response.text.strip()  
            driver_url = f'https://chromedriver.storage.googleapis.com/{version}/{driver_name}'  
            response = requests.get(driver_url)  
            with open(webdriver_path, 'wb') as file:  
                file.write(response.content)  
            print(f'Webdriver baixado com sucesso para {system}.')  
        except Exception as e:  
            print(f'Falha ao baixar o webdriver: {str(e)}')  
    else:  
        print(f'O webdriver para {system} já existe.')  
  
    return webdriver_path