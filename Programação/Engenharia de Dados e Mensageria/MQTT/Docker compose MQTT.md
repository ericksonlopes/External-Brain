# Infraestrutura e Padrões MQTT (VerneMQ)

Este arquivo descreve a infraestrutura baseada no broker **VerneMQ** e os padrões básicos de comunicação (Pub/Sub).

## 1. Broker MQTT com Docker Compose
O VerneMQ é um broker robusto e escalável. A configuração abaixo expõe as portas principais para comunicação local.

- **Portas:** `1884` (MQTT TCP), `8080` (Web Admin), `8083` (WebSockets).
- **Segurança:** Configurado para permitir conexões anônimas em desenvolvimento.

```yml
version: "3.7"  
  
services:  
  vernemq:  
    image: vernemq/vernemq:latest  
    container_name: vernemq  
    restart: unless-stopped  
    ports:  
      - "1884:1883" # MQTT TCP (porta padrão)  
      - "8080:8080" # Web Admin UI (opcional)  
      - "8083:8083" # WebSocket sem TLS (para frontend)  
    environment:  
      - DOCKER_VERNEMQ_ALLOW_ANONYMOUS=on  # Permite conexões anônimas  
      - DOCKER_VERNEMQ_LISTENER__TCP__DEFAULT=0.0.0.0:1883  
      - DOCKER_VERNEMQ_LISTENER__WS__DEFAULT=0.0.0.0:8083  
      - DOCKER_VERNEMQ_LISTENER__WS__MOUNTPOINT=/  
      - DOCKER_VERNEMQ_ACCEPT_EULA=yes
```

## 2. Padrão Subscriber (Assinante)
O Assinante fica em "escuta" contínua. Utiliza `loop_forever()` para manter o script bloqueado processando mensagens recebidas.

```python
import paho.mqtt.client as mqtt  
  
BROKER = "localhost"  
PORTA = 1884  
TOPICO = "sensores/temperatura"  
  
def on_connect(client, userdata, flags, rc):  
    if rc == 0:  
        print("✅ Conectado ao broker MQTT!")  
        client.subscribe(TOPICO)  
    else:  
        print(f"❌ Erro na conexão. Código: {rc}")  
  
def on_message(client, userdata, msg):  
    print(f"📥 Mensagem: {msg.payload.decode()} no tópico '{msg.topic}'")  
  
cliente = mqtt.Client()  
cliente.on_connect = on_connect  
cliente.on_message = on_message  
  
cliente.connect(BROKER, PORTA)  
  
try:  
    cliente.loop_forever()  
except KeyboardInterrupt:  
    cliente.disconnect()  
```

## 3. Padrão Publisher (Publicador)
O Publicador conecta, envia uma mensagem e encerra a conexão. Ideal para envio esporádico de dados de sensores.

```python
import random  
import time  
import paho.mqtt.client as mqtt  
  
BROKER = "localhost"  
PORTA = 1884  
TOPICO = "sensores/temperatura"  
  
def on_publish(client, userdata, mid):  
    print(f"📤 Mensagem enviada! (ID: {mid})")  
  
cliente = mqtt.Client()  
cliente.on_publish = on_publish  
  
cliente.connect(BROKER, PORTA)  
cliente.loop_start()  
  
time.sleep(1)  # Aguarda conexão estabilizar
  
mensagem = f"{random.randint(1, 10)}°C"  
resultado = cliente.publish(TOPICO, mensagem)  
resultado.wait_for_publish()  
  
cliente.loop_stop()  
cliente.disconnect()  
```
