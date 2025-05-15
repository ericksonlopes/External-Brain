
#### Pré-requisitos
1. **Docker** e **Docker Compose** instalados.
2. **Python 3.8+** instalado.
3. Clonar o repositório do projeto ou ter os arquivos necessários (`docker-compose.yml`, `.env`, `app.py`, `waha.py`).

---

#### Passo 1: Configurar o ambiente
1. Crie um arquivo `.env` com as variáveis de ambiente necessárias. Exemplo:
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

2. Certifique-se de que o arquivo `.gitignore` está configurado para ignorar o `.env` e outros arquivos sensíveis.

---

#### Passo 2: Subir os serviços com Docker Compose
1. Execute o comando para iniciar os serviços:
   ```bash
   docker-compose up -d
   ```

2. Verifique se os containers estão rodando:
   ```bash
   docker ps
   ```

---

#### Passo 3: Configurar o webhook
1. O arquivo `app.py` já está configurado para receber eventos no endpoint `/webhook`.
2. Certifique-se de que o `WHATSAPP_HOOK_URL` no `.env` aponta para o endereço correto do webhook.

---

#### Passo 4: Testar o bot
1. Execute o arquivo `app.py`:
   ```bash
   python app.py
   ```

2. Envie uma mensagem para o número configurado no WhatsApp e veja o bot responder.

---

#### Passo 5: Logs e Debug
1. Para verificar os logs do WAHA:
   ```bash
   docker logs <container_id> -f
   ```

2. Para verificar os logs do Flask:
   Veja o console onde o `app.py` está rodando.

---

#### Passo 6: Parar os serviços
1. Para parar os containers:
   ```bash
   docker-compose down
   ```

---

Este tutorial pode ser reutilizado para configurar e rodar o WAHA em outros ambientes.