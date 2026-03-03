#rabbitmq #fastapi #python #uvicorn

# Integração RabbitMQ com FastAPI

Esta implementação demonstra como integrar um consumidor RabbitMQ em uma aplicação **FastAPI**, utilizando **Threads** para evitar o bloqueio do servidor web.

### Estratégia de Implementação:
- **Multithreading**: O consumidor RabbitMQ é executado em uma thread separada (`threading.Thread`). Isso é necessário porque o `start_consuming()` do Pika é bloqueante e impediria o FastAPI de processar requisições HTTP.
- **Evento de Startup**: O decorador `@app.on_event("startup")` garante que o consumidor inicie automaticamente assim que a API for ligada.
- **Daemon Thread**: Ao definir `daemon=True`, a thread do consumidor é encerrada automaticamente quando o processo principal do FastAPI for interrompido.

```python
import threading
import pika
from fastapi import FastAPI
import uvicorn

# Função do Consumidor
def start_rabbitmq_consumer():
    # Configuração de Conexão
    credentials = pika.PlainCredentials('USUARIO', 'SENHA')
    parameters = pika.ConnectionParameters(
        host='SEU_HOST',
        port=5672,
        virtual_host='rabbitmq',
        credentials=credentials
    )
    
    connection = pika.BlockingConnection(parameters)
    channel = connection.channel()

    # Garante a existência da fila (Quorum Queue)
    channel.queue_declare(
        queue='messages.upsert',
        durable=True,
        arguments={'x-queue-type': 'quorum'}
    )

    def callback(ch, method, properties, body):
        print(f"📥 Received message: {body.decode()}")
    
    # Inicia o consumo
    channel.basic_consume(
        queue='messages.upsert', 
        on_message_callback=callback, 
        auto_ack=True
    )
    
    print('🚀 RabbitMQ Consumer: Waiting for messages...')
    channel.start_consuming()

# Inicialização da API
app = FastAPI()

# Inicia o consumidor no startup da aplicação
@app.on_event("startup")
def startup_event():
    # Executa o consumidor em background para não bloquear a API
    threading.Thread(target=start_rabbitmq_consumer, daemon=True).start()

@app.get("/")
async def read_root():
    return {"status": "API online e Consumidor RabbitMQ rodando em background"}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=9000)
```
