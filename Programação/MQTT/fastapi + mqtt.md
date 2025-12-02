#mqtt #fastAPI #python

```python
# mqtt_subscribes.py
_subscribe_callbacks = []

def mqtt_subscribe(topic: str):
    def decorator(func):
        _subscribe_callbacks.append((topic, func))
        return func
    return decorator

def register_all_subscribes(mqtt_client: MQTTClient):
    for topic, callback in _subscribe_callbacks:
        mqtt_client.subscribe(topic, callback)


# chat_subscribe.py
from mqtt_subscribes import mqtt_subscribe

@mqtt_subscribe("/chat")
def handle_chat(msg, token):
    print(msg, token)


# lifespan.py (FastAPI setup)
from contextlib import asynccontextmanager
from src.infrastructure.services.mqtt_client_service import MQTTClient
from mqtt_subscribes import register_all_subscribes

@asynccontextmanager
async def lifespan(app: FastAPI):
    mqttc = MQTTClient("localhost", 1884, origin=settings.MQTT_ORIGIN)
    with mqttc:
        register_all_subscribes(mqttc)
        app.state.mqttc = mqttc
        yield

```



