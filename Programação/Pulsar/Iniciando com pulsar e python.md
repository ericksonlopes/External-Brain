#pulsar #python #pulsar-client

## Docker compose

```yml
version: '3.8'
services:
  standalone:
    image: apachepulsar/pulsar:latest
    ports:
      - 8080:8080
      - 6650:6650
    command: bin/pulsar standalone

```

```bash
pip install pulsar-client
```

## Producer

```python
import pulsar
client = pulsar.Client('pulsar://localhost:6650')
producer = client.create_producer('my-topic')
producer.send(b'Hello Pulsar!')
client.close()
```
## Consumer

```python
import pulsar
client = pulsar.Client('pulsar://localhost:6650')
consumer = client.subscribe('my-topic', subscription_name='my-sub')
msg = consumer.receive()
print(msg.data())
consumer.acknowledge(msg)
client.close()
```