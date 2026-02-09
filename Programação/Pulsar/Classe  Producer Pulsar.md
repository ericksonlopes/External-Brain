#producer #python #pulsar #pulsar-client 

```python
from typing import Optional, Any  
  
import pulsar  
import logging

from src.domain.services.pulsar_service_interface import IPulsarProducerService  
from src.infrastructure.logger.logger import Logger  

  
class PulsarProducerService:  
    def __init__(self, service_url: str, topic: str):  
        super().__init__()  
  
        self.service_url = service_url  
        self.topic = topic  
        self.client: Optional[pulsar.Client] = None  
        self.producer: Optional[pulsar.Producer] = None  
  
    def __enter__(self):  
        try:  
            self.client = pulsar.Client(self.service_url)  
            self.producer = self.client.create_producer(self.topic)  
            logging.info(f"Producer connected to {self.topic}")  
            return self  
        except Exception as e:  
            logging.error(e, context={"service_url": self.service_url, "topic": self.topic})  
            raise  
  
    def __exit__(self, exc_type, exc_value, traceback):  
        try:  
            if self.producer:  
                self.producer.close()  
            if self.client:  
                self.client.close()  
        except Exception as e:  
            logging.error(e)  
  
    def send(self, data: Any, key: Optional[str] = None) -> bool:  
        if not self.producer:  
            raise RuntimeError("Producer not connected. Use within 'with'")  
        try:  
            if isinstance(data, dict):  
                data_bytes = str(data).encode()  
            elif isinstance(data, str):  
                data_bytes = data.encode()            
            else:  
                data_bytes = data            msg = self.producer.send(data_bytes, key)  
            logging.debug(f"Message sent: {msg}")  
            return True  
        except Exception as e:  
            logging.error(f"Error sending: {e}")  
            return False
```