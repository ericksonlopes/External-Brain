# 🐘 PostgreSQL com Docker Compose

Template otimizado para rodar o **PostgreSQL** em ambientes de desenvolvimento, incluindo volumes persistentes e healthchecks.

---

## 📄 Docker Compose (`docker-compose.yml`)

```yaml
services:
  postgres:
    image: postgres:15-alpine
    container_name: local_postgres
    restart: always
    environment:
      POSTGRES_DB: meu_banco
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret_password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      # Script inicial de criação de tabelas (opcional)
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d meu_banco"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

---

## 💡 Notas Técnicas

- **Persistência**: O volume `postgres_data` garante que seus dados não sejam perdidos ao deletar o container.
- **Healthcheck**: Essencial se você tem outros serviços (como uma API) que dependem do banco estar pronto para iniciar.
- **Acesso Externo**: Conecte via ferramentas como DBeaver ou pgAdmin usando `localhost:5432`.

---
#docker #postgresql #sql #database #backend #devops
