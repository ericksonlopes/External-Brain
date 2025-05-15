# Tutorial: Como Rodar o WAHA (WhatsApp HTTP API) Localmente

Este tutorial detalha como configurar e rodar o WAHA localmente, além de demonstrar como interagir com ele usando Python. Ele serve como uma documentação pessoal para referência futura.

---

## 1. Estrutura Mínima de Pastas/Arquivos

A estrutura mínima necessária para rodar o WAHA é a seguinte:

```
waha_project/
├── .env
├── docker-compose.yml
├── app.py
└── waha.py
```

### Arquivo `.env`
Este arquivo contém as variáveis de ambiente necessárias para configurar o WAHA e o banco de dados PostgreSQL.

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

### Arquivo `docker-compose.yml`
Este arquivo define os serviços Docker necessários para rodar o WAHA e o PostgreSQL.

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

---

## 2. Como Iniciar o WAHA Localmente

1. **Instale o Docker e o Docker Compose**:
   Certifique-se de que o Docker e o Docker Compose estão instalados no seu sistema.

2. **Inicie os serviços**:
   No terminal, navegue até a pasta do projeto e execute:
   ```bash
   docker-compose up
   ```

3. **Verifique se o WAHA está rodando**:
   Acesse `http://localhost:3000` no navegador. Você deve ver a interface do WAHA.

---

## 3. Como Configurar o Ambiente

- Certifique-se de que o arquivo `.env` está configurado corretamente.
- O webhook (`WHATSAPP_HOOK_URL`) deve apontar para o endpoint do seu servidor Flask (ex.: `http://host.docker.internal:5000/webhook`).

---

## 4. Como se Conectar e Enviar Mensagens Usando Python

### Arquivo `waha.py`
Este arquivo contém uma classe para interagir com a API do WAHA.

```python
import requests


class Waha:

    def __init__(self):
        self.__api_url = 'http://localhost:3000'

    def send_message(self, chat_id, message):
        url = f'{self.__api_url}/api/sendText'
        headers = {
            'Content-Type': 'application/json',
        }
        payload = {
            'session': 'default',
            'chatId': chat_id,
            'text': message,
        }
        requests.post(
            url=url,
            json=payload,
            headers=headers,
        )
```

### Arquivo `app.py`
Este arquivo implementa um webhook para receber mensagens e responder automaticamente.

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
        waha.send_message(
            chat_id=chat_id,
            message="Você me enviou: " + received_message,
        )

    return jsonify({'status': 'success'}), 200


if __name__ == '__main__':
    app.run(host="127.0.0.1", port=5000, debug=True)
```

---

## 5. Como Saber se o WAHA Está Funcionando Corretamente

- **Verifique os logs**:
  No terminal, os logs do Docker devem mostrar que o WAHA está rodando sem erros.

- **Teste a API**:
  Use o comando abaixo para testar a API:
  ```bash
  curl -X POST http://localhost:3000/api/sendText \
       -H "Content-Type: application/json" \
       -d '{"session": "default", "chatId": "5511940708204@g.us", "text": "Olá, mundo!"}'
  ```

- **Webhook**:
  Envie uma mensagem para o número configurado no WhatsApp e veja se o webhook responde corretamente.

---

## 6. Erros Comuns e Como Resolvê-los

1. **Erro de conexão com o PostgreSQL**:
   - Certifique-se de que o serviço `postgres` está rodando.
   - Verifique as credenciais no arquivo `.env`.

2. **Webhook não recebe mensagens**:
   - Verifique se o `WHATSAPP_HOOK_URL` está configurado corretamente.
   - Certifique-se de que o servidor Flask está rodando.

3. **WAHA não inicia**:
   - Verifique os logs do Docker para identificar o problema.
   - Certifique-se de que as portas `3000` e `5432` estão livres.

---

Com este tutorial, você deve ser capaz de configurar, rodar e interagir com o WAHA de forma prática e eficiente.