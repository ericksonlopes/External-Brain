# 🚀 Airflow Standalone: Início Rápido

O modo **Standalone** é a maneira mais fácil de rodar o Airflow localmente para desenvolvimento e testes, sem precisar configurar Docker ou bancos de dados complexos manualmente.

## 🛠️ Instalação e Execução

### 1. Criar ambiente virtual
```bash
python -m venv venv
source venv/bin/activate # Linux
venv\Scripts\activate # Windows
```

### 2. Instalar Airflow
```bash
pip install "apache-airflow[celery]" --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.7.1/constraints-3.10.txt"
```

### 3. Rodar o comando Standalone
```bash
export AIRFLOW_HOME=$(pwd)/airflow # Opcional: define onde os arquivos ficarão
airflow standalone
```

## 🔑 Acesso
O comando `standalone` fará o seguinte automaticamente:
1.  Inicializar o banco de dados (SQLite por padrão).
2.  Criar um usuário administrador.
3.  **Gerar uma senha** (exibida no terminal).
4.  Iniciar o Webserver em `http://localhost:8080`.

## 📁 Onde colocar minhas DAGs?
Por padrão, o Airflow procurará por DAGs na pasta `~/airflow/dags`. Você pode alterar isso no arquivo `airflow.cfg`.

---
#airflow #python #data-engineering #tutorial
