#python #rabbitmq

pip install pika
```python
import pika

credentials = pika.PlainCredentials('ADMINRABBITMQ', '!v1n4pDTiVh1kL')
parameters = pika.ConnectionParameters(
    host='72.60.155.136',
    port=5672,
    virtual_host='rabbitmq',
    credentials=credentials
)
connection = pika.BlockingConnection(parameters)
channel = connection.channel()

channel.queue_declare(
    queue='messages.upsert',
    durable=True,
    arguments={'x-queue-type': 'quorum'}
)


def callback(ch, method, properties, body):
    print(f"Received message: {body}")


channel.basic_consume(queue='messages.upsert', on_message_callback=callback, auto_ack=True)
print('Waiting for messages...')
channel.start_consuming()
```