#python #rabbitmq

# RabbitMQ com Python (Pika)

Esta implementação demonstra o uso básico da biblioteca **Pika** para conectar a um broker RabbitMQ e consumir mensagens de uma fila persistente.

### Conceitos Chave:
- **`BlockingConnection`**: Cria uma conexão síncrona com o broker.
- **`queue_declare`**: Garante que a fila exista. O parâmetro `durable=True` garante que a fila sobreviva a reinicializações do broker.
- **`x-queue-type: quorum`**: Define a fila como uma fila de quórum, ideal para alta disponibilidade e consistência.
- **`basic_consume`**: Registra uma função de callback para processar as mensagens recebidas.

```python
# Instalação: pip install pika

import pika

# Configurações de Acesso
credentials = pika.PlainCredentials('ADMINRABBITMQ', '!v1n4pDTiVh1kL')
parameters = pika.ConnectionParameters(
    host='72.60.155.136',
    port=5672,
    virtual_host='rabbitmq',
    credentials=credentials
)

# Estabelece conexão e cria canal
connection = pika.BlockingConnection(parameters)
channel = connection.channel()

# Declaração da fila com persistência e tipo Quorum
channel.queue_declare(
    queue='messages.upsert',
    durable=True,
    arguments={'x-queue-type': 'quorum'}
)

# Função de processamento de mensagens
def callback(ch, method, properties, body):
    print(f"📥 Mensagem recebida: {body.decode()}")

# Configuração do consumo
channel.basic_consume(
    queue='messages.upsert', 
    on_message_callback=callback, 
    auto_ack=True # Confirmação automática de recebimento
)

print('🚀 Aguardando mensagens... Pressione Ctrl+C para sair.')
channel.start_consuming()
```
