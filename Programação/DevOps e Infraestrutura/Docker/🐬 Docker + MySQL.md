# 🐬 MySQL com Docker e SQLAlchemy

Este guia mostra como subir um banco de dados MySQL via Docker e realizar a conexão utilizando Python com a ORM **SQLAlchemy**.

---

## 🏗️ Configuração do Container (Dockerfile)

Se preferir criar uma imagem customizada com credenciais pré-definidas:

```dockerfile
FROM mysql:latest

# Configurações de Ambiente
ENV MYSQL_ROOT_PASSWORD=root_password
ENV MYSQL_DATABASE=meu_banco_core
ENV MYSQL_USER=admin_user
ENV MYSQL_PASSWORD=test_password

EXPOSE 3306
```

---

## 🐍 Conexão via Python (SQLAlchemy)

### Instalação
```bash
pip install sqlalchemy pymysql
```

### Script de Exemplo
```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import declarative_base, Session

# 1. Configurar Engine (Driver pymysql)
DATABASE_URL = "mysql+pymysql://admin_user:test_password@localhost:3306/meu_banco_core"
engine = create_engine(DATABASE_URL)

# 2. Definir Modelo
Base = declarative_base()

class User(Base):
    __tablename__ = 'users'
    id = Column(Integer, primary_key=True)
    name = Column(String(50))
    age = Column(Integer)

# 3. Operações de Banco
Base.metadata.create_all(bind=engine) # Cria tabela se não existir
session = Session(engine)

# Adicionar
session.add(User(name='John Doe', age=30))
session.commit()

# Consultar
for user in session.query(User).all():
    print(f"User: {user.name}")

session.close()
```

---
#docker #mysql #sql #python #sqlalchemy #orm #database
