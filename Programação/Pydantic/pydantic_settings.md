```python
from loguru import logger  
from pydantic_settings import BaseSettings  
  
  
class Settings(BaseSettings):  
    OPENAI_API_KEY: str  
  
    class Config:  
        env_file = ".env"  
  
  
settings = Settings()  
  
logger.add("file.log", rotation="500 MB", level="INFO")  
logger.add(lambda msg: print(msg), level="DEBUG")
```

```env
OPENAI_API_KEY
```