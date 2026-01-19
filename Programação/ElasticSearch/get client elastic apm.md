```python
import threading  
from elasticapm import Client, get_client  
from elasticapm.contrib.starlette import ElasticAPM, make_apm_client  
from fastapi import FastAPI  
  
from src.config.settings import settings  
  
  
class ElasticAPMManager:  
    _client_instance: Client | None = None  
    _lock = threading.Lock()  
  
    def __init__(self):  
        self.client = self._get_or_create_apm_client()  
  
    @classmethod  
    def _get_or_create_apm_client(cls) -> Client | None:  
        """  
        Returns the singleton instance of the APM client.        Creates it only once and reuses it for all instances.        Uses a lock to ensure thread-safety.        """        # First, check if we already have an instance  
        if cls._client_instance is not None:  
            return cls._client_instance  
  
        # Try to get the global ElasticAPM client (if already created)  
        try:  
            existing_client = get_client()            if existing_client is not None:  
                cls._client_instance = existing_client  
                return existing_client  
        except Exception:  
            # If no global client exists, continue to create a new one  
            pass  
  
        # Use lock to ensure only one thread creates the client  
        with cls._lock:  
            # Check again after acquiring the lock (double-check)  
            if cls._client_instance is not None:  
                return cls._client_instance  
  
            # Try to get the global client again (it may have been created by another thread)  
            try:  
                existing_client = get_client()                if existing_client is not None:  
                    cls._client_instance = existing_client  
                    return existing_client  
            except Exception:  
                pass  
  
            # Create the client only if it still does not exist  
            cls._client_instance = cls._create_apm_client()  
            return cls._client_instance  
  
    @classmethod  
    def _create_apm_client(cls) -> Client | None:  
        if not settings.ELASTIC_APM_SERVER_URL:  
            return None  
  
        return make_apm_client({  
            "SERVICE_NAME": settings.ELASTIC_APM_SERVICE_NAME,  
            "API_KEY": settings.ELASTIC_APM_API_KEY,  
            "SERVER_URL": settings.ELASTIC_APM_SERVER_URL,  
            "ENVIRONMENT": settings.ELASTIC_APM_ENVIRONMENT,  
        })  
    def add_apm_middleware(self, app: FastAPI) -> None:  
        if self.client:  
            app.add_middleware(ElasticAPM, client=self.client)
```