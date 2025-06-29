# 🚀 Dica de Python: Sua primeira aplicação com LangGraph

## Aprenda a construir um chatbot simples utilizando LangGraph e OpenAI

# Introdução

Neste artigo, vamos explorar como criar sua primeira aplicação com LangGraph, uma biblioteca poderosa que facilita a construção de gráficos de estados para chatbots. Usaremos o modelo de linguagem GPT-4 da OpenAI para dar vida ao nosso chatbot. Vamos por etapas!

# Pré-requisitos

Antes de começarmos, certifique-se de ter:

- Python instalado (recomendamos a versão 3.8 ou superior).
- Uma chave de API da OpenAI, que você pode obter ao se registrar no site da OpenAI.
- As bibliotecas `langchain` e `langgraph` instaladas. Você pode instalá-las usando o pip:

```bash
pip install "langchain[openai]" langgraph
```

# Estrutura do Projeto

Vamos primeiro entender a estrutura básica do nosso projeto. Usaremos as seguintes importações:

```python
import os
from typing import Annotated
from langchain.chat_models import init_chat_model
from langchain_core.messages import HumanMessage
from langgraph.constants import START, END
from langgraph.graph import StateGraph
from langgraph.graph.message import add_messages
from typing_extensions import TypedDict
```

## Configurando a Chave da API

O primeiro passo é definir a chave da API do OpenAI. Isso é crucial para que possamos acessar os modelos de linguagem:

```python
# Define a chave da API do OpenAI
os.environ["OPENAI_API_KEY"] = "sk-..."
```

## Definindo o Tipo de Estado

Usaremos um `TypedDict` para definir o tipo de estado que nosso gráfico de estados irá manipular:

```python
# Define o tipo de estado para o gráfico de estados
class State(TypedDict):
    messages: Annotated[list, add_messages]
```

## Criando o Grafo de Estados

Agora, vamos criar um construtor de gráfico de estados e inicializar o modelo de chat com o GPT-4:

```python
# Cria um construtor de gráfico de estados
graph_builder = StateGraph(State)

# Inicializa o modelo de chat com o OpenAI GPT-4
llm = init_chat_model("openai:gpt-4o")
```

## Função do Chatbot

A função do chatbot invoca o modelo com as mensagens do estado. Aqui está como podemos implementá-la:

```python
# Função do chatbot que invoca o modelo com as mensagens do estado
def chatbot(state: State):
    return {"messages": [llm.invoke(state["messages"])]}
```

## Adicionando Nós e Arestas

Com o modelo e a função prontos, vamos adicionar nós e arestas ao gráfico:

```python
# Adiciona as arestas e nós ao gráfico
graph_builder.add_edge(START, "chatbot")
graph_builder.add_node("chatbot", chatbot)
graph_builder.add_edge("chatbot", END)
```

## Compilando o Fluxo de Trabalho

Agora, compilamos o fluxo de trabalho e preparamos nosso chatbot para perguntas:

```python
# Compila o fluxo de trabalho
workflow = graph_builder.compile()
```

## Fazendo uma Pergunta

Por fim, vamos fazer uma pergunta ao nosso chatbot e imprimir a resposta:

```python
# Pergunta para o chatbot
ask = "Olá, tudo bem?"
resultado = workflow.invoke({"messages": [HumanMessage(content=ask)]})

# Imprime a resposta do chatbot
print(resultado["messages"][-1].content)
```

# Conclusão

Parabéns! Você criou sua primeira aplicação de chatbot utilizando LangGraph e OpenAI. Essa estrutura básica pode ser expandida com mais funcionalidades, como gerenciamento de contexto, integração com APIs externas e muito mais.

# Boas Práticas e Dicas
- Sempre proteja suas chaves de API e não as exponha em repositórios públicos.
- Teste diferentes prompts e interações para melhorar a eficácia do seu chatbot.
- Explore a documentação das bibliotecas utilizadas para aprofundar seu conhecimento.

# Referências
- [Documentação do LangGraph](https://langgraph.readthedocs.io)
- [OpenAI API](https://beta.openai.com/docs/)

Espero que você tenha achado este artigo útil e inspirador para suas futuras aplicações com Python e LangGraph!

## Exemplos de Código

### Exemplo
```python
import os
from typing import Annotated
from langchain.chat_models import init_chat_model
from langchain_core.messages import HumanMessage
from langgraph.constants import START, END
from langgraph.graph import StateGraph
from langgraph.graph.message import add_messages
from typing_extensions import TypedDict

os.environ["OPENAI_API_KEY"] = "sk-..."

class State(TypedDict):
    messages: Annotated[list, add_messages]

graph_builder = StateGraph(State)
llm = init_chat_model("openai:gpt-4o")

def chatbot(state: State):
    return {"messages": [llm.invoke(state["messages"])]}

graph_builder.add_edge(START, "chatbot")
graph_builder.add_node("chatbot", chatbot)
graph_builder.add_edge("chatbot", END)

workflow = graph_builder.compile()

ask = "Olá, tudo bem?"
resultado = workflow.invoke({"messages": [HumanMessage(content=ask)]})
print(resultado["messages"][-1].content)
```

## Referências

- https://langgraph.readthedocs.io
- https://beta.openai.com/docs/

---
*Este artigo foi escrito para ajudar desenvolvedores a começarem com LangGraph e chatbots utilizando Python.*

---
#Python #LangGraph #langchain #langchain_openai #openai 