# 🐰 RabbitMQ com Docker Compose

Template para rodar o **RabbitMQ** com o plugin de **Management** ativado, permitindo monitorar filas e trocas via interface web.

---

## 📄 Configuração (`docker-compose.yml`)

```yaml
services:
  rabbitmq:
    image: rabbitmq:management
    container_name: rabbitmq_broker
    restart: always
    ports:
      - "5672:5672"   # Protocolo AMQP (Código)
      - "15672:15672" # Management Plugin (Web UI)
    environment:
      RABBITMQ_DEFAULT_USER: admin
      RABBITMQ_DEFAULT_PASS: admin_password
    volumes:
      - rabbitmq_data:/var/lib/rabbitmq/
      - rabbitmq_logs:/var/log/rabbitmq

volumes:
  rabbitmq_data:
  rabbitmq_logs:
```

---

## 📊 Interface de Gerenciamento

Após subir o container, você pode acessar o dashboard em:
👉 **URL**: `http://localhost:15672`
👉 **Login**: Credenciais definidas no arquivo yaml.

## 🐍 Conexão via Python (Pika)

```python
import pika

connection = pika.BlockingConnection(
    pika.ConnectionParameters(
        host='localhost',
        credentials=pika.PlainCredentials('admin', 'admin_password')
    )
)
channel = connection.channel()
channel.queue_declare(queue='minha_fila')
```

---
#docker #rabbitmq #mensageria #queue #python
