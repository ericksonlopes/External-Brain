#python #IA #langchain #sql #openai #langchain_community #langchain_openai

```python
import os
import sqlite3

import yfinance as yf
from langchain import hub
from langchain.agents import create_react_agent, AgentExecutor
from langchain_community.agent_toolkits.sql.toolkit import SQLDatabaseToolkit
from langchain_community.utilities.sql_database import SQLDatabase
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI

from config import OPENAI_KEY

os.environ["OPENAI_API_KEY"] = OPENAI_KEY

model = ChatOpenAI(
    model="gpt-4o"
)

if not os.path.exists("stocks.db"):
    symbol = 'MSFT'

    ticket = yf.Ticker(symbol)
    df = ticket.history(period="max")
    df.reset_index(inplace=True)

    df['symbol'] = symbol

    conn = sqlite3.connect('stocks.db')

    df.to_sql('stock_prices', conn, if_exists='replace')

    conn.close()

db = SQLDatabase.from_uri("sqlite:///stocks.db")

toolkit = SQLDatabaseToolkit(
    db=db,
    llm=model
)

system_message = hub.pull("hwchase17/react")

agent = create_react_agent(
    llm=model,
    tools=toolkit.get_tools(),
    prompt=system_message
)

agent_executor = AgentExecutor(
    agent=agent,
    tools=toolkit.get_tools(),
    verbose=True
)

prompt = """
Use as ferramentas necessarias para responder perguntas relacionadas ao historico de ações ao longo dos anos.
Responda tudo em brasileiro.
Perguntas: {q}
"""

prompt_template = PromptTemplate.from_template(prompt)

question = "Qual foi a maxima e a minima da ação MSFT no ano de 2020?"

output = agent_executor.invoke(
    {"input": prompt_template.format(q=question)}
)

print(output.get("output"))
```

## Explicação do Código

Este script em Python cria um agente inteligente capaz de responder perguntas sobre o histórico de ações, especificamente da Microsoft (MSFT), utilizando dados reais obtidos através da API do **yfinance** e armazenados em um banco de dados SQLite. O agente é configurado para responder em português brasileiro e utiliza as capacidades do modelo **GPT-4** para interpretar a pergunta e consultar o banco de dados para fornecer a resposta.

### 1. **Importações e Configurações Iniciais**


```python
import os
import sqlite3
import yfinance as yf
from langchain import hub
from langchain.agents import create_react_agent, AgentExecutor
from langchain_community.agent_toolkits.sql.toolkit import SQLDatabaseToolkit
from langchain_community.utilities.sql_database import SQLDatabase
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI
from config import OPENAI_KEY

os.environ["OPENAI_API_KEY"] = OPENAI_KEY
```
- **Bibliotecas Importadas**:
    - `os` e `sqlite3` para manipulação de arquivos e bancos de dados.
    - `yfinance` para obter dados financeiros da internet.
    - Várias bibliotecas do **LangChain** para criar e gerenciar agentes de linguagem natural.
- **Configuração da Chave de API**:
    - A chave de API da OpenAI é importada de um arquivo de configuração e definida como variável de ambiente para uso posterior.

### 2. **Inicialização do Modelo de Linguagem**


```python
model = ChatOpenAI(model="gpt-4o")
```

- **ChatOpenAI**: Inicializa o modelo de linguagem GPT-4 otimizado para chat, que será usado pelo agente para processar linguagem natural.

### 3. **Criação do Banco de Dados com Dados de Ações**


```python
if not os.path.exists("stocks.db"):
    symbol = 'MSFT'
    ticket = yf.Ticker(symbol)
    df = ticket.history(period="max")
    df.reset_index(inplace=True)
    df['symbol'] = symbol
    conn = sqlite3.connect('stocks.db')
    df.to_sql('stock_prices', conn, if_exists='replace')
    conn.close()
```

- **Verificação do Banco de Dados**: O código verifica se o arquivo `stocks.db` já existe para evitar recriação desnecessária.
- **Obtendo Dados com yfinance**:
    - `yf.Ticker(symbol)` cria um objeto para o símbolo de ação especificado (MSFT).
    - `ticket.history(period="max")` obtém todo o histórico de preços disponível.
- **Preparação dos Dados**:
    - `df.reset_index(inplace=True)` redefine os índices do DataFrame para garantir que as datas sejam tratadas corretamente.
    - `df['symbol'] = symbol` adiciona uma coluna com o símbolo da ação.
- **Criação do Banco de Dados SQLite**:
    - Conecta-se ou cria o banco de dados `stocks.db`.
    - Salva o DataFrame `df` na tabela `stock_prices`.
    - Fecha a conexão com o banco de dados.

### 4. **Configuração do Banco de Dados para o Agente**


```python
db = SQLDatabase.from_uri("sqlite:///stocks.db")
```

- **SQLDatabase**: Conecta o agente ao banco de dados SQLite usando a URI fornecida.

### 5. **Configuração das Ferramentas do Agente**

python

Copy code

```python
toolkit = SQLDatabaseToolkit(db=db, llm=model)
```

- **SQLDatabaseToolkit**: Fornece ao agente ferramentas para interagir com o banco de dados SQL usando linguagem natural.

### 6. **Criação do Agente com Prompt REACT**



```python
system_message = hub.pull("hwchase17/react")
agent = create_react_agent(llm=model, tools=toolkit.get_tools(), prompt=system_message)
```

- **Obtenção do Prompt REACT**:
    - `hub.pull("hwchase17/react")` obtém um prompt pré-definido do repositório LangChain, que ajuda o agente a estruturar suas respostas.
- **Criação do Agente**:
    - `create_react_agent` cria um agente que utiliza o modelo de linguagem, as ferramentas SQL e o prompt especificado.

### 7. **Configuração do Executor do Agente**



```python
agent_executor = AgentExecutor(agent=agent, tools=toolkit.get_tools(), verbose=True)
```

- **AgentExecutor**: Configura um executor para o agente que gerencia a execução de consultas e interações.
- **Parâmetro `verbose=True`**: Ativa a saída detalhada, útil para depuração e compreensão do processo interno.

### 8. **Definição do Prompt e da Pergunta**


```python
prompt = """ 
Use as ferramentas necessárias para responder perguntas relacionadas ao histórico de ações ao longo dos anos. 
Responda tudo em brasileiro. 
Perguntas: {q} 
"""

prompt_template = PromptTemplate.from_template(prompt)
question = "Qual foi a maxima e a minima da ação MSFT no ano de 2020?"
```

- **PromptTemplate**:
    - Cria um template de prompt que instrui o agente sobre como responder.
    - Enfatiza que as respostas devem ser em português brasileiro.
- **Pergunta Específica**:
    - A pergunta visa descobrir a máxima e a mínima das ações da MSFT no ano de 2020.

### 9. **Execução da Pergunta e Obtenção da Resposta**



```python
output = agent_executor.invoke({"input": prompt_template.format(q=question)})
print(output.get("output"))
```

- **Invoke**:
    - Executa o agente com a entrada fornecida, que é a pergunta formatada no prompt.
- **Impressão da Resposta**:
    - Extrai e imprime a resposta gerada pelo agente.

### 10. **Fluxo Geral do Programa**

- **Preparação**:
    - Importa bibliotecas e configurações necessárias.
    - Obtém dados financeiros e configura o banco de dados.
- **Configuração do Agente**:
    - Inicializa o modelo de linguagem e configura o agente com as ferramentas e prompts adequados.
- **Interação**:
    - Define o prompt e a pergunta.
    - O agente processa a pergunta, interage com o banco de dados se necessário, e gera uma resposta.
- **Resultado**:
    - A resposta é impressa, fornecendo a máxima e a mínima das ações da MSFT em 2020.

---

## Como o Agente Funciona

- **Interpretação da Pergunta**: O agente usa o modelo de linguagem GPT-4 para entender a pergunta em linguagem natural.
    
- **Consulta ao Banco de Dados**:
    
    - Utiliza as ferramentas SQL para formular e executar uma consulta ao banco de dados SQLite com os dados das ações.
        
    - Exemplo de consulta que o agente pode gerar:
        
        `SELECT MAX(High) as Maxima, MIN(Low) as Minima FROM stock_prices WHERE symbol='MSFT' AND Date BETWEEN '2020-01-01' AND '2020-12-31';`
        
- **Geração da Resposta**:
    
    - Processa os resultados da consulta.
    - Gera uma resposta em português brasileiro, conforme instruído no prompt.

## Observações Importantes

- **Segurança da Chave de API**:
    - A chave de API da OpenAI é importada de um arquivo de configuração (`config.py`). Certifique-se de que este arquivo não seja compartilhado publicamente.
- **Dependências**:
    - O código depende de várias bibliotecas que devem ser instaladas previamente, como `yfinance`, `langchain`, `sqlite3`, entre outras.
- **Expansibilidade**:
    - O agente pode ser facilmente adaptado para trabalhar com outros símbolos de ações ou para responder a diferentes tipos de perguntas relacionadas a dados financeiros.
