#selenium #python #capabilities

```python
from selenium import webdriver  
from selenium.webdriver.chrome.webdriver import WebDriver  
  
from config import URL_WEBDRIVER  
  
  
def get_webdriver() -> WebDriver:  
	options = webdriver.ChromeOptions()  
	  
	options.default_capabilities['goog:chromeOptions'] = {'args': [  
			'--headless',  
			'--no-sandbox',  
			'--disable-dev-shm-usage',  
			'--disable-gpu'  
			]  
		}  
  
	return webdriver.Remote(command_executor=URL_WEBDRIVER, options=options)
```