# 📡 MQTT Client — Pub/Sub com Paho MQTT v5 e Python

Implementação completa de um client MQTT v5 usando `paho-mqtt`, com suporte a **shared subscriptions**, **autenticação via token** (Keycloak), **ThreadPoolExecutor** para processamento assíncrono e um sistema de **decorators para registrar subscribers**.

## 🏗️ Arquitetura

```
┌──────────────────────┐
│ MqttSubscriptionHandler │  ← Registro de subscribers via decorator
│  @mqtt_subscribe       │
└──────────┬─────────────┘
           │ register_all_subscribes()
┌──────────▼─────────────┐     ┌──────────────┐
│      MQTTClient        │────▶│  MQTT Broker  │
│  - connect / disconnect│     │  (Mosquitto)  │
│  - subscribe / publish │     └──────────────┘
│  - token validation    │
│  - ThreadPoolExecutor  │
└────────────────────────┘
```

## 🔧 Dependências

```bash
pip install paho-mqtt
```

## 📄 Implementação

### 1. Subscription Handler (Decorator Pattern)

Sistema de registro de subscribers via decorator — similar ao `@app.route` do Flask.

```python
class MqttSubscriptionHandler:
    def __init__(self):
        self._subscribe_callbacks = []

    def subscribe(self, topic: str):
        """Decorator para registrar um callback para um tópico."""
        def decorator(func):
            self._subscribe_callbacks.append((topic, func))
            return func
        return decorator

    def get_subscribe_callbacks(self):
        return self._subscribe_callbacks

    def register_all_subscribes(self, mqtt_client):
        """Registra todos os callbacks decorados no client MQTT."""
        for topic, factory in self.get_subscribe_callbacks():
            callback = factory(mqtt_client)
            mqtt_client.subscribe(topic, callback)


# Singleton e atalho para o decorator
mqtt_subscription_handler = MqttSubscriptionHandler()
mqtt_subscribe = mqtt_subscription_handler.subscribe
```

#### Uso do Decorator

```python
@mqtt_subscribe("meu/topico")
def handle_meu_topico(mqtt_client):
    def callback(message: dict):
        print(f"Mensagem recebida: {message}")
    return callback
```

### 2. MQTT Client (MQTTv5 + Shared Subscriptions)

```python
import json
from concurrent.futures import ThreadPoolExecutor
from typing import Any, Dict, Optional, Callable

import paho.mqtt.client as mqtt
from paho.mqtt.client import CallbackAPIVersion, MQTTv5
from paho.mqtt.packettypes import PacketTypes
from paho.mqtt.properties import Properties

MessageCallback = Callable[[dict], None]


class MQTTClient:
    def __init__(
        self,
        broker_ip: str,
        broker_port: int,
        origin: str = "",
        use_loop_forever: bool = False,
        client_id: Optional[str] = None,
        max_workers: Optional[int] = None,
    ) -> None:
        self.client = mqtt.Client(
            CallbackAPIVersion.VERSION2,
            client_id=client_id or "",
            protocol=MQTTv5,
            reconnect_on_failure=True,
        )
        self.client.reconnect_delay_set(min_delay=0, max_delay=120)
        self.client.on_connect = self.on_connect
        self.client.on_disconnect = self.on_disconnect
        self.client.on_message = self._internal_on_message

        self.broker_ip = broker_ip
        self.broker_port = broker_port
        self.origin = origin.rstrip("/")
        self.use_loop_forever = use_loop_forever
        self._topic_callbacks: Dict[str, MessageCallback] = {}
        self.executor = ThreadPoolExecutor(max_workers=max_workers)

    # ── Lifecycle ────────────────────────────────────────────

    def on_connect(self, client, userdata, flags, reason_code, properties):
        if reason_code == 0:
            for topic in self._topic_callbacks:
                topic_to_sub = f"$share/api/{topic}" if not topic.startswith("$share") else topic
                client.subscribe(topic_to_sub, qos=1)

    def on_disconnect(self, client, userdata, flags, reason_code, properties):
        pass  # auto-reconexão habilitada

    def connect(self, keepalive: int = 60) -> None:
        self.client.connect(self.broker_ip, self.broker_port, keepalive=keepalive, clean_start=False)
        if self.use_loop_forever:
            self.client.loop_forever(retry_first_connection=True)
        else:
            self.client.loop_start()

    def disconnect(self) -> None:
        self.executor.shutdown(wait=True)
        self.client.loop_stop()
        self.client.disconnect()

    # ── Topic helpers ────────────────────────────────────────

    def _build_topic(self, topic: str) -> str:
        topic = topic.lstrip("/")
        return f"{self.origin}/{topic}" if self.origin else topic

    def _strip_origin(self, topic: str) -> str:
        if not self.origin:
            return topic
        prefix = self.origin.rstrip("/") + "/"
        return topic[len(prefix):] if topic.startswith(prefix) else topic

    # ── Subscribe / Publish ──────────────────────────────────

    def subscribe(self, topic: str, callback: MessageCallback, qos: int = 1) -> None:
        full_topic = self._build_topic(topic)
        share_topic = f"$share/api/{full_topic}"

        self._topic_callbacks[full_topic] = callback
        stripped = self._strip_origin(full_topic)
        if stripped != full_topic:
            self._topic_callbacks[stripped] = callback

        self.client.subscribe(share_topic, qos=qos)

    def publish(self, topic: str, message: dict, qos: int = 1, token: Optional[str] = None) -> None:
        full_topic = self._build_topic(topic)
        payload = json.dumps(message)

        props: Optional[Properties] = None
        if token is not None:
            props = Properties(PacketTypes.PUBLISH)
            props.UserProperty = [("token", token)]

        self.client.publish(full_topic, payload=payload, qos=qos, properties=props)

    # ── Internal message handler ─────────────────────────────

    def _internal_on_message(self, client, userdata, msg: mqtt.MQTTMessage) -> None:
        stripped = self._strip_origin(msg.topic)
        callback = self._topic_callbacks.get(msg.topic) or self._topic_callbacks.get(stripped)
        if not callback:
            return

        try:
            parsed = json.loads(msg.payload.decode("utf-8"))
        except (json.JSONDecodeError, UnicodeDecodeError):
            return

        self.executor.submit(callback, parsed)
```

### 3. Uso Completo

```python
import uuid

# Criar e conectar
mqtt_client = MQTTClient(
    broker_ip="localhost",
    broker_port=1883,
    origin="minha-app",
    client_id=f"minha-app-{uuid.uuid4().hex[:8]}",
)
mqtt_client.connect()

# Registrar subscribers decorados
mqtt_subscription_handler.register_all_subscribes(mqtt_client)

# Publicar mensagem
mqtt_client.publish(
    topic="eventos/novo",
    message={"evento": "usuario_criado", "id": 123},
    token="jwt-token-aqui",
)

# Desconectar
mqtt_client.disconnect()
```

## ⚙️ Conceitos Principais

| Conceito | Descrição |
|---|---|
| **MQTTv5** | Versão 5 do protocolo — suporta `UserProperty`, shared subscriptions, etc. |
| **Shared Subscriptions** | `$share/group/topic` — distribui mensagens entre múltiplas instâncias (load balancing) |
| **`origin`** | Prefixo automático nos tópicos (ex: `minha-app/eventos/novo`) |
| **`ThreadPoolExecutor`** | Callbacks executam em threads separadas — não bloqueiam o loop do MQTT |
| **`clean_start=False`** | Mantém sessão persistente — mensagens offline são entregues ao reconectar |
| **Token via `UserProperty`** | Autenticação por JWT enviada como propriedade MQTT v5 |

## 📝 Notas

- O `on_connect` re-subscriba automaticamente todos os tópicos após reconexão.
- O `reconnect_delay_set(0, 120)` faz backoff exponencial até 2 minutos.
- O código de produção inclui validação de token via Keycloak no `_internal_on_message`.
- Para FastAPI, use `use_loop_forever=False` (loop_start em background thread).

---
#python #mqtt #paho #pub-sub #iot #messaging
