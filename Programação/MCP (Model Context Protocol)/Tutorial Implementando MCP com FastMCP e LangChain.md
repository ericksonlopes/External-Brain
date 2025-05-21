#python #mcp #langchain #langchain_community #langchain_openai #fastmcp #openai 
  

Este tutorial demonstra como criar uma aplicação usando o padrão Model-Controller-Presenter (MCP) com FastMCP e LangChain em Python.

  
## 📋 Pré-requisitos

  

Primeiro, instale as dependências necessárias:

  

```bash

pip install fastmcp langchain langchain-community langchain-openai

```

  

## 🏗️ Estrutura do Projeto

  

O projeto consiste em dois arquivos principais:

- `server.py`: Implementa o servidor MCP

- `client.py`: Implementa o cliente que se comunica com o servidor

  

## 🔧 Implementação do Servidor (server.py)

  

```python

from fastmcp import FastMCP

  

# Inicializa o servidor MCP

server = FastMCP("Servidor de Teste")

server.dependencies = []

  

# Define uma ferramenta (tool) no servidor

@server.tool()

def somar(a: int, b: int) -> int:
    """Soma dois números inteiros. Exemplo: '8 10'"""
    if not isinstance(a, int) or not isinstance(b, int):
        raise ValueError("Os parâmetros devem ser números inteiros.")
    return a + b

  

if __name__ == "__main__":
    server.run()

```

  

## 💻 Implementação do Cliente (client.py)

  

```python

import os
import subprocess
from langchain.agents import initialize_agent, AgentType
from langchain.chat_models import ChatOpenAI
from langchain.tools import Tool

  
# Configuração da chave da API OpenAI
os.environ["OPENAI_API_KEY"] = "sua-chave-api-aqui"


def chamar_mcp_ferramenta(input_str: str) -> str:
    """Chama o servidor MCP via subprocess e envia input_str"""
    process = subprocess.Popen(
        ['python', 'server.py'],
        stdin=subprocess.PIPE,

        stdout=subprocess.PIPE,

        stderr=subprocess.PIPE,

        text=True

    )

    stdout, stderr = process.communicate(input_str)

    return stdout.strip()

  

# Configuração das ferramentas disponíveis

tools = [

    Tool(

        name="Somar",

        func=lambda x: chamar_mcp_ferramenta(f"somar {x}"),

        description="Soma dois números inteiros. Exemplo: '8 10'"

    )

]

  

# Inicialização do agente LangChain

llm = ChatOpenAI(temperature=0)

agent = initialize_agent(

    tools,

    llm,

    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,

    verbose=True

)

  

# Exemplo de uso

if __name__ == "__main__":

    pergunta = "Qual é a soma de 10 e 4?"

    resposta = agent.run(pergunta)

    print("\n🤖 Resposta:", resposta)

```

  

## 🚀 Como Executar

  

1. Inicie o servidor MCP:

```bash

fastmcp install .\server.py

fastmcp dev .\server.py

```

  

2. Em outro terminal, execute o cliente:

```bash

python3 .\client.py

```

  

## 📝 Explicação do Código

  

### Servidor MCP

- Utiliza `FastMCP` para criar um servidor

- Define ferramentas (tools) usando o decorador `@server.tool()`

- Cada ferramenta é uma função Python com tipagem definida

- O servidor processa as requisições e retorna os resultados

  

### Cliente LangChain

- Utiliza `subprocess` para comunicação com o servidor MCP

- Define ferramentas LangChain que encapsulam as funções do servidor

- Utiliza `ChatOpenAI` para processamento de linguagem natural

- Implementa um agente que pode usar as ferramentas definidas

  

## 🔍 Pontos Importantes

  

1. **Tipagem**: Todas as funções devem ter tipos bem definidos

2. **Documentação**: Inclua docstrings explicativas

3. **Tratamento de Erros**: Implemente validações adequadas

4. **Segurança**: Mantenha sua chave API segura

  

## 🎯 Casos de Uso

  

Este padrão é útil para:

- Integração de LLMs com funções Python

- Criação de APIs conversacionais

- Automação de tarefas complexas

- Desenvolvimento de assistentes virtuais

  

## 📚 Recursos Adicionais

  

- [Documentação FastMCP](https://github.com/fastmcp/fastmcp)

- [Documentação LangChain](https://python.langchain.com/docs/get_started/introduction)

- [Documentação OpenAI](https://platform.openai.com/docs/api-reference)

  

## ⚠️ Considerações de Segurança

  

- Nunca exponha sua chave API

- Valide todas as entradas

- Implemente rate limiting quando necessário

- Monitore o uso de recursos

  

## 🔄 Próximos Passos

  

1. Adicione mais ferramentas ao servidor

2. Implemente autenticação

3. Adicione logging

4. Implemente testes unitários

5. Configure monitoramento

  

---

  

Este tutorial foi criado para servir como referência rápida para implementações futuras de MCP com LangChain. Sinta-se à vontade para contribuir e melhorar este documento!