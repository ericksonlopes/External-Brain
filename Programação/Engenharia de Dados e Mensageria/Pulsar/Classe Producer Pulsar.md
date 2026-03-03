# 📡 Apache Pulsar: Classe Producer Service

O Apache Pulsar é uma plataforma de mensageria e streaming distribuída. Este guia apresenta uma classe utilitária para enviar mensagens de forma eficiente e segura usando o padrão **Context Manager** (`with`).

## 🛠️ Instalação

```bash
pip install pulsar-client
```

## 📄 Implementação do Producer

```python
import pulsar
import logging
from typing import Optional, Any

logger = logging.getLogger(__name__)

class PulsarProducerService:
    def __init__(self, service_url: str, topic: str):
        self.service_url = service_url
        self.topic = topic
        self.client: Optional[pulsar.Client] = None
        self.producer: Optional[pulsar.Producer] = None

    def __enter__(self):
        """Inicializa conexão ao entrar no bloco 'with'."""
        try:
            self.client = pulsar.Client(self.service_url)
            self.producer = self.client.create_producer(self.topic)
            logger.info(f"Conectado ao tópico: {self.topic}")
            return self
        except Exception as e:
            logger.error(f"Erro ao conectar ao Pulsar: {e}")
            raise

    def send(self, data: Any, key: Optional[str] = None) -> bool:
        """Envia dados (dict, str ou bytes)."""
        if not self.producer:
            raise RuntimeError("Producer não conectado.")
        try:
            # Serialização simples
            if isinstance(data, dict):
                data_bytes = str(data).encode()
            elif isinstance(data, str):
                data_bytes = data.encode()
            else:
                data_bytes = data

            msg_id = self.producer.send(data_bytes, partition_key=key)
            logger.debug(f"Mensagem enviada ID: {msg_id}")
            return True
        except Exception as e:
            logger.error(f"Erro no envio: {e}")
            return False

    def __exit__(self, exc_type, exc_value, traceback):
        """Fecha conexões ao sair do bloco."""
        if self.producer: self.producer.close()
        if self.client: self.client.close()
```

## 🚀 Como Usar

```python
URL = "pulsar://localhost:6650"
TOPIC = "persistent://public/default/meu-topico"

with PulsarProducerService(URL, TOPIC) as producer:
    producer.send({"evento": "venda", "valor": 100.50}, key="venda-001")
```

---
#pulsar #mensageria #streaming #python #data-engineering
