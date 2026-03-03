#mqtt #fastAPI #python

# Integração MQTT com FastAPI

Esta implementação demonstra como integrar um cliente MQTT ao ciclo de vida de uma aplicação **FastAPI**, utilizando decoradores personalizados para registro dinâmico de assinaturas.

## 1. O Padrão Decorator (`@mqtt_subscribe`)
Para evitar registrar assinaturas manualmente em vários lugares, utilizamos um decorador que armazena as funções de callback em uma lista centralizada.

```python
# mqtt_subscribes.py
_subscribe_callbacks = []

def mqtt_subscribe(topic: str):
    def decorator(func):
        # Armazena o par (tópico, função) para registro posterior
        _subscribe_callbacks.append((topic, func))
        return func
    return decorator

def register_all_subscribes(mqtt_client: MQTTClient):
    # Percorre a lista e registra no cliente MQTT
    for topic, callback in _subscribe_callbacks:
        mqtt_client.subscribe(topic, callback)
```

## 2. Uso nos Controladores
As funções de negócio são decoradas diretamente com o tópico que devem escutar.

```python
# chat_subscribe.py
from mqtt_subscribes import mqtt_subscribe

@mqtt_subscribe("/chat")
def handle_chat(msg, token):
    print(f"Mensagem de chat recebida: {msg}")
```

## 3. Gerenciamento do Ciclo de Vida (Lifespan)
O FastAPI utiliza o `lifespan` para gerenciar recursos globais. Aqui, o cliente MQTT é iniciado no startup e encerrado no shutdown da aplicação.

```python
# lifespan.py (Configuração do FastAPI)
from contextlib import asynccontextmanager
from fastapi import FastAPI
from mqtt_subscribes import register_all_subscribes

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Inicialização: Conecta e registra todos os tópicos
    mqttc = MQTTClient("localhost", 1884)
    with mqttc:
        register_all_subscribes(mqttc)
        app.state.mqttc = mqttc # Armazena para uso em rotas HTTP
        yield
    # Finalização: O Context Manager do MQTTClient fecha a conexão automaticamente
```
