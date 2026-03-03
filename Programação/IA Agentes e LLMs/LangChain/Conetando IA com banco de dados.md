# 🤖 SQL Agent: Conectando LLM a Bancos de Dados

Este guia mostra como criar um agente inteligente que utiliza o framework **LangChain** para converter perguntas em linguagem natural em consultas SQL, executá-las em um banco de dados e retornar a resposta.

---

## 🛠️ Instalação

```bash
pip install langchain yfinance langchain-openai langchain-community
```

---

## 📄 Fluxo ReAct (Reason + Act)

O agente utiliza o padrão **ReAct**, onde ele:
1.  **Pensa** sobre a pergunta.
2.  **Age** escolhendo uma ferramenta (ex: consultar o schema do banco).
3.  **Observa** o resultado e repete o processo até ter a resposta final.

### Exemplo: Consultando Preços de Ações (MSFT)

```python
import os
import sqlite3
from langchain.agents import create_react_agent, AgentExecutor
from langchain_community.agent_toolkits.sql.toolkit import SQLDatabaseToolkit
from langchain_community.utilities.sql_database import SQLDatabase
from langchain_openai import ChatOpenAI

# 1. Preparar Banco de Dados SQLite
db = SQLDatabase.from_uri("sqlite:///stocks.db")

# 2. Configurar o Modelo e as Ferramentas SQL
llm = ChatOpenAI(model="gpt-4o")
toolkit = SQLDatabaseToolkit(db=db, llm=llm)

# 3. Criar o Agente
agent = create_react_agent(
    llm=llm, 
    tools=toolkit.get_tools(), 
    prompt=hub.pull("hwchase17/react")
)

# 4. Execução
executor = AgentExecutor(agent=agent, tools=toolkit.get_tools(), verbose=True)
question = "Qual foi a cotação máxima da MSFT em 2020?"
executor.invoke({"input": f"Responda em português: {question}"})
```

## 📝 Por que usar um Agente SQL?

- **Sem SQL manual**: O usuário não precisa conhecer a estrutura das tabelas.
- **Precisão**: O LLM consulta os dados reais em vez de "alucinar" informações.
- **Flexibilidade**: Funciona com SQLite, PostgreSQL, MySQL e qualquer banco compatível com SQLAlchemy.

---
#ai #langchain #sql #openai #agent #database #python
