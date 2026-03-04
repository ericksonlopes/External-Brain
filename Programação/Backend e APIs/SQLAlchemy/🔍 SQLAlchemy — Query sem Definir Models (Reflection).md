Técnica para consultar tabelas existentes no banco **sem criar classes ORM**. O SQLAlchemy usa **reflection** (`autoload_with`) para carregar a estrutura da tabela diretamente do banco.

## 🔧 Dependências

```bash
pip install sqlalchemy
# + driver do banco (ex: snowflake-sqlalchemy, psycopg2, etc.)
```

## 📄 Implementação

```python
from datetime import datetime, timedelta
from sqlalchemy import create_engine, MetaData, Table
from sqlalchemy.orm import Session

DATABASE_URL = "snowflake://{USER}:{PASSWORD}@{ACCOUNT}/{DATABASE}/{SCHEMA}"

engine = create_engine(DATABASE_URL, echo=False)

# MetaData armazena a estrutura refletida das tabelas
metadata = MetaData()

# Reflection — carrega colunas, tipos e constraints do banco
nome_tabela = Table('nome_tabela', metadata, autoload_with=engine)

now = datetime.now()
now_minus_24h = now - timedelta(hours=24)

with Session(engine) as session:
    query = (
        nome_tabela
        .select()
        .where(nome_tabela.c.date.between(now_minus_24h, now))
    )

    results = session.execute(query).fetchall()
    print(results)
```

## ⚙️ Como Funciona

| Etapa | Descrição |
|---|---|
| `MetaData()` | Container que armazena metadados das tabelas |
| `Table(..., autoload_with=engine)` | Conecta ao banco e carrega a estrutura da tabela automaticamente |
| `nome_tabela.c.date` | Acessa a coluna `date` via `.c` (columns) |
| `.select().where(...)` | Monta a query usando Core API (não ORM) |

## 💡 Quando Usar

- **Bancos legados** onde você não quer/pode criar models ORM
- **Scripts pontuais** de consulta ou migração de dados
- **Data warehouses** (Snowflake, BigQuery) onde o schema muda frequentemente
- **Exploração** rápida de tabelas desconhecidas

## 📝 Notas

- O `autoload_with` faz um `SELECT` no `information_schema` na primeira chamada — pode ser lento em bancos com muitas tabelas.
- Para queries mais complexas (joins, subqueries), use `sqlalchemy.select()` do Core API.
- Acessar colunas: `tabela.c.nome_coluna` ou `tabela.columns['nome_coluna']`.

---
#python #sqlalchemy #sql #orm #reflection