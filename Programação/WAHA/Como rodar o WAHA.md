Para rodar o WAHA com um exemplo simples em Python, siga os passos abaixo:

### 1. **Configurar o ambiente**
Certifique-se de ter o Docker e o Docker Compose instalados no seu sistema.

### 2. **Criar os arquivos necessários**
Crie os seguintes arquivos no mesmo diretório:

#### `.env`
```dotenv
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=waha_db

WHATSAPP_SESSIONS_POSTGRESQL_URL=postgres://postgres:postgres@postgres:5432/waha_db?sslmode=disable
WAHA_MEDIA_STORAGE=POSTGRESQL
WAHA_MEDIA_POSTGRESQL_URL=postgres://postgres:postgres@postgres:5432/waha_db?sslmode=disable
WHATSAPP_FILES_FOLDER=/app/.media
WHATSAPP_FILES_LIFETIME=0
WAHA_WORKER_RESTART_SESSIONS=True
TZ=America/Sao_Paulo

WAHA_SESSION=default
WHATSAPP_HOOK_URL=http://host.docker.internal:5000/webhook
WHATSAPP_HOOK_EVENTS=*
```

#### `docker-compose.yml`
```yaml
services:
  waha:
    image: devlikeapro/waha
    ports:
      - "3000:3000"
    env_file:
      - .env
    volumes:
      - ./sessions:/app/.sessions
      - ./media:/app/.media
    depends_on:
      - postgres

  postgres:
    image: postgres:17
    environment:
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=${POSTGRES_DB}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    command:
      - postgres
      - "-c"
      - "max_connections=3000"

volumes:
  postgres_data:
```

#### `app.py`
```python
from flask import Flask, request, jsonify
from waha import Waha

app = Flask(__name__)

@app.route("/webhook", methods=["POST"])
def whatsapp_webhook():
    data = request.get_json()

    if data["payload"]["from"] == 'status@broadcast':
        return jsonify({'status': 'success'}), 200

    if data["event"] == "message" and not data["payload"]['fromMe']:
        chat_id = data["payload"]["from"]
        received_message = data["payload"]["body"]

        waha = Waha()
        waha.start_typing(chat_id=chat_id)
        waha.send_message(chat_id=chat_id, message="Você me enviou: " + received_message)
        waha.stop_typing(chat_id=chat_id)

    return jsonify({'status': 'success'}), 200

if __name__ == '__main__':
    app.run(host="127.0.0.1", port=5000, debug=True)
```

#### `waha.py`
```python
import requests

class Waha:
    def __init__(self):
        self.__api_url = 'http://localhost:3000'

    def send_message(self, chat_id, message):
        url = f'{self.__api_url}/api/sendText'
        headers = {'Content-Type': 'application/json'}
        payload = {'session': 'default', 'chatId': chat_id, 'text': message}
        requests.post(url=url, json=payload, headers=headers)

    def start_typing(self, chat_id):
        url = f'{self.__api_url}/api/startTyping'
        headers = {'Content-Type': 'application/json'}
        payload = {'session': 'default', 'chatId': chat_id}
        requests.post(url=url, json=payload, headers=headers)

    def stop_typing(self, chat_id):
        url = f'{self.__api_url}/api/stopTyping'
        headers = {'Content-Type': 'application/json'}
        payload = {'session': 'default', 'chatId': chat_id}
        requests.post(url=url, json=payload, headers=headers)
```

### 3. **Subir os serviços**
No terminal, execute:
```bash
docker-compose up
```

### 4. **Testar o webhook**
Envie uma mensagem para o número configurado no WhatsApp e veja o bot responder automaticamente.

Este exemplo simples configura o WAHA com um bot básico que responde mensagens recebidas.