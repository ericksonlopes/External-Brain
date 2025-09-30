#rabbitmq #fastapi #python #uvicorn

```python
import threading
import pika
from fastapi import FastAPI
import uvicorn

def start_rabbitmq_consumer():
    credentials = pika.PlainCredentials('', '')
    parameters = pika.ConnectionParameters(
        host='',
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

app = FastAPI()

@app.on_event("startup")
def startup_event():
    threading.Thread(target=start_rabbitmq_consumer, daemon=True).start()

@app.get("/")
async def read_root():
    return {"Hello": "World"}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=9000)

```