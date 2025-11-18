```python
import paho.mqtt.client as mqtt

  

class MQTTClient:

    def __init__(self, broker_ip, broker_port, client_id=""):

        self.client = mqtt.Client(client_id)

        self.broker_ip = broker_ip

        self.broker_port = broker_port

        self.client.on_connect = self.on_connect

        self.client.on_message = self.on_message

  

    def __enter__(self):

        self.connect()

        return self

  

    def __exit__(self, exc_type, exc_val, exc_tb):

        self.disconnect()

  

    def on_connect(self, client, userdata, flags, rc):

        if rc == 0:

            print("Conectado com sucesso")

        else:

            print(f"Falha na conexão, código {rc}")

  

    def on_message(self, client, userdata, message):

        print(f"Mensagem recebida '{message.payload.decode()}' no tópico '{message.topic}'")

  

    def connect(self):

        self.client.connect(self.broker_ip, self.broker_port)

        self.client.loop_start()

  

    def subscribe(self, topic):

        self.client.subscribe(topic)

  

    def publish(self, topic, payload):

        self.client.publish(topic, payload)

  

    def disconnect(self):

        self.client.loop_stop()

        self.client.disconnect()

  
  

with MQTTClient("localhost", 1884) as mqttc:

    # mqttc.subscribe("topico/teste")

    mqttc.publish("topico/teste", "mensagem de teste")
```