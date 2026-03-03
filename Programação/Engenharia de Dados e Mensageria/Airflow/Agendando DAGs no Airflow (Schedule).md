
O agendamento de DAGs é o que permite a automação de pipelines de dados. No Airflow, isso é feito através do parâmetro `schedule_interval`.

## ⚙️ Opções de Agendamento

### 1. Expressões Cron (Padrão)
O Airflow aceita strings no formato crontab padrão:
```python
dag = DAG(
    'minha_dag',
    schedule_interval='0 12 * * *', # Roda todo dia ao meio-dia
    start_date=datetime(2024, 1, 1),
)
```

### 2. Atalhos Pré-definidos
- `@once`: Executa apenas uma vez.
- `@hourly`: Todo início de hora.
- `@daily`: À meia-noite de cada dia.
- `@weekly`: À meia-noite de domingo.
- `@monthly`: À meia-noite do primeiro dia do mês.

### 3. Objetos Timedelta
Para intervalos fixos de tempo:
```python
from datetime import timedelta
dag = DAG(
    'minha_dag',
    schedule_interval=timedelta(minutes=30), # Roda a cada 30 min
)
```

## 📝 Boas Práticas
- **`start_date`**: Use uma data fixa no passado, evite `datetime.now()`.
- **`catchup`**: Defina como `False` se você não quiser que o Airflow tente executar todas as datas passadas acumuladas.

---
#airflow #python #data-engineering #schedule #dag
