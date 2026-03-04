# 📊 Elastic APM Client — Singleton Thread-Safe com FastAPI

Manager para criar e reutilizar uma única instância do client Elastic APM em aplicações FastAPI. Usa o padrão **Singleton com double-check locking** para garantir thread-safety.

## 🔧 Dependências

```bash
pip install elastic-apm fastapi
```

## ⚙️ Variáveis de Configuração

O client espera as seguintes variáveis no objeto `settings`:

| Variável | Descrição |
|---|---|
| `ELASTIC_APM_SERVER_URL` | URL do servidor APM |
| `ELASTIC_APM_SERVICE_NAME` | Nome do serviço monitorado |
| `ELASTIC_APM_API_KEY` | Chave de autenticação |
| `ELASTIC_APM_ENVIRONMENT` | Ambiente (`dev`, `prod`, etc.) |

## 📄 Implementação

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
        Returns the singleton instance of the APM client.
        Creates it only once and reuses it for all instances.
        Uses a lock to ensure thread-safety.
        """
        if cls._client_instance is not None:
            return cls._client_instance

        try:
            existing_client = get_client()
            if existing_client is not None:
                cls._client_instance = existing_client
                return existing_client
        except Exception:
            pass

        with cls._lock:
            if cls._client_instance is not None:
                return cls._client_instance

            try:
                existing_client = get_client()
                if existing_client is not None:
                    cls._client_instance = existing_client
                    return existing_client
            except Exception:
                pass

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

## 🚀 Uso

```python
from fastapi import FastAPI

app = FastAPI()
apm_manager = ElasticAPMManager()
apm_manager.add_apm_middleware(app)
```

## 📝 Notas

- Se `ELASTIC_APM_SERVER_URL` estiver vazia, o client **não é criado** (fail-safe).
- O padrão **double-check locking** evita race conditions na criação do client.
- O middleware intercepta automaticamente requests HTTP para gerar traces no APM.

---
#python #elasticsearch #apm #fastapi #observability