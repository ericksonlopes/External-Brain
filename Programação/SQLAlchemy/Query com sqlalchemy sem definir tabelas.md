#sqlalchemy #sql #orm #python

```python
from datetime import datetime, timedelta

from sqlalchemy import create_engine, MetaData, Table
from sqlalchemy.orm import Session

USER = ""
PASSWORD = ""
ACCOUNT = ""
DATABASE = ""
SCHEMA = ""

DATABASE_URL = f'snowflake://{USER}:{PASSWORD}@{ACCOUNT}/{DATABASE}/{SCHEMA}'

# Criando engine com o banco de conexão
engine = create_engine(DATABASE_URL, echo=False)

# Criar um objeto MetaData
metadata = MetaData()

nome_tabela = Table('nome_tabela', metadata, autoload_with=engine)

now = datetime.now()
now_minus_24h = now - timedelta(hours=24)

list_titles = []

with Session(engine) as session:
    query = (
        nome_tabela
        .select()
        .where(nome_tabela.c.date.between(now_minus_24h, now))
        # .where(break_news_bronze.c.id == 1499)
    )

    # Executar a consulta
    results = session.execute(query).fetchall()

    print(results)
```


