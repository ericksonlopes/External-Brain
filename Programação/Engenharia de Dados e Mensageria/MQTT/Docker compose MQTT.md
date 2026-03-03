#mqtt #python #docker-compose 


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
      - DOCKER_VERNEMQ_ALLOW_ANONYMOUS=on  # Permite conexões anônimas (sem autenticação)  
      - DOCKER_VERNEMQ_LISTENER__TCP__DEFAULT=0.0.0.0:1883  
      - DOCKER_VERNEMQ_LISTENER__WS__DEFAULT=0.0.0.0:8083  
      - DOCKER_VERNEMQ_LISTENER__WS__MOUNTPOINT=/  
      - DOCKER_VERNEMQ_ACCEPT_EULA=yes
```

# Subscriber

```python
import paho.mqtt.client as mqtt  
  
# Configurações do broker  
BROKER = "localhost"  
PORTA = 1884  
TOPICO = "sensores/temperatura"  
  
  
  
# Callback quando conecta  
def on_connect(client, userdata, flags, rc):  
    if rc == 0:  
        print("✅ Conectado ao broker MQTT!")  
        # Inscreve no tópico ao conectar  
        client.subscribe(TOPICO)  
        print(f"📡 Aguardando mensagens no tópico '{TOPICO}'...")  
    else:  
        print(f"❌ Erro na conexão. Código: {rc}")  
  
  
# Callback quando recebe mensagem  
def on_message(client, userdata, msg):  
    print("📥 Mensagem recebida!")  
    print(f"   Tópico: {msg.topic}")  
    print(f"   Mensagem: {msg.payload.decode()}")  
    print("-" * 40)  
  
  
# Cria cliente  
cliente = mqtt.Client()  
cliente.on_connect = on_connect  
cliente.on_message = on_message  
  
# Conecta  
cliente.connect(BROKER, PORTA)  
  
# Mantém escutando (loop infinito)  
print("Pressione Ctrl+C para parar...")  
try:  
    cliente.loop_forever()  
except KeyboardInterrupt:  
    print("\nDesconectando...")  
    cliente.disconnect()  
    print("Desconectado.")
```

# Publisher

```python
import random  
import time  
  
import paho.mqtt.client as mqtt  
  
# Configurações do broker  
BROKER = "localhost"  
PORTA = 1884  
TOPICO = "sensores/temperatura"  
  
  
# Callback quando conecta  
def on_connect(client, userdata, flags, rc):  
    if rc == 0:  
        print("✅ Conectado ao broker MQTT!")  
    else:  
        print(f"❌ Erro na conexão. Código: {rc}")  
  
  
# Callback quando publica  
def on_publish(client, userdata, mid):  
    print(f"📤 Mensagem enviada! (ID: {mid})")  
  
  
# Cria cliente  
cliente = mqtt.Client()  
cliente.on_connect = on_connect  
cliente.on_publish = on_publish  
  
# Conecta  
cliente.connect(BROKER, PORTA)  
cliente.loop_start()  
  
# Aguarda conexão  
time.sleep(1)  
  
# Envia mensagem  
mensagem = f"{random.randint(1, 10)}°C"  
print(f"Enviando: '{mensagem}' para o tópico '{TOPICO}'...")  
resultado = cliente.publish(TOPICO, mensagem)  
resultado.wait_for_publish()  
  
time.sleep(1)  
  
# Desconecta  
cliente.loop_stop()  
cliente.disconnect()  
print("Desconectado.")
```