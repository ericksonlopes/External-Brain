
```python
import weaviate  
from weaviate.classes.init import Auth  
  
from src.config.logger import Logger  
  
logger = Logger()  
  
  
class WeaviateConnector:  
    def __init__(self, cluster_url: str, api_key: str):  
        self.cluster_url = cluster_url  
        self.api_key = api_key  
        self._client = None  
  
    def _create_client(self):  
        try:  
            # client = weaviate.connect_to_weaviate_cloud(  
            #     cluster_url=self.cluster_url,            #     auth_credentials=Auth.api_key(self.api_key),            # )  
            client = weaviate.connect_to_local(  
                host="localhost",  # ou o nome do serviço no Docker, ex: "weaviate"  
                port=8080,  
                grpc_port=50051,  
            )            
            return client  
        except Exception as e:  
            logger.error(f"Error creating Weaviate connection: {e}")  
            raise  
  
    def __enter__(self):  
        """Context manager entry."""  
        self._client = self._create_client()  
        return self._client  
  
    def __exit__(self, exc_type, exc_val, exc_tb):  
        """Context manager exit."""  
        if self._client is not None:  
            try:  
                self._client.close()  
            except Exception as e:  
                logger.error(f"Error closing Weaviate connection: {e}")  
            finally:  
                self._client = None  
  
        if exc_type is not None:  
            logger.error(f"Error during Weaviate operation: {exc_val}")
```