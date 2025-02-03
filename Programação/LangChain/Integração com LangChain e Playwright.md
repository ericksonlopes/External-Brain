#python #langchain #langchain_openai #langchain_community #playwright #IA 

Este código demonstra a integração do LangChain com o Playwright e OpenAI GPT para coletar as últimas notícias de um site (G1) por meio de um agente de IA.

## Componentes Principais:
- **`os`**: Utilizado para configurar variáveis de ambiente, como a chave da API do OpenAI.
- **`decouple.config`**: Carrega variáveis de configuração de um arquivo `.env`, onde a chave da API do OpenAI é armazenada.
- **`langchain.agents`**: Fornece funcionalidades para trabalhar com agentes no LangChain.
- **`langchain_community.agent_toolkits.PlayWrightBrowserToolkit`**: Toolkit do LangChain para utilizar o Playwright, que permite controlar o navegador e coletar dados.
- **`langchain_openai.ChatOpenAI`**: Interface para a OpenAI, configurando o modelo GPT-4 mini.
- **`create_sync_playwright_browser`**: Função que cria uma instância do navegador Playwright para realizar a navegação e extração de dados.

## Passos do Código:

1. **Configuração da Chave da API**:
   - A chave da API do OpenAI é carregada de um arquivo `.env` utilizando `config('OPENAI_API_KEY')`.

2. **Inicialização do Modelo LLM (Large Language Model)**:
   - O modelo `gpt-4o-mini` é configurado com uma temperatura de `0`, o que significa que o modelo será mais determinístico em suas respostas.

3. **Configuração do Navegador Playwright**:
   - O Playwright é configurado para permitir a automação de navegação e coleta de dados do site.
   - O `PlayWrightBrowserToolkit` é utilizado para configurar o Playwright e permitir que o agente use as ferramentas necessárias para interagir com o navegador.

4. **Inicialização do Agente**:
   - O agente é inicializado com o tipo `STRUCTURED_CHAT_ZERO_SHOT_REACT_DESCRIPTION`, o que permite ao agente realizar tarefas de coleta de informações e reação a descrições sem necessidade de treinamento adicional.
   - O modelo LLM e as ferramentas do Playwright são fornecidos ao agente.

5. **Execução da Tarefa**:
   - O agente é invocado com uma entrada solicitando as últimas notícias do site "https://g1.globo.com/ultimas-noticias/".
   - O resultado da execução é impresso.

## Função do Código:
Esse código permite que você automatize a coleta de dados de um site, utilizando IA para interpretar e gerar respostas com base nas informações extraídas da web.


```python 
import os  
  
from decouple import config  
from langchain.agents import AgentType, initialize_agent  
from langchain_community.agent_toolkits import PlayWrightBrowserToolkit  
from langchain_community.tools.playwright.utils import create_sync_playwright_browser  
from langchain_openai import ChatOpenAI  
  
os.environ['OPENAI_API_KEY'] = config('OPENAI_API_KEY')  
  
if __name__ == '__main__':  
    llm = ChatOpenAI(  
        model="gpt-4o-mini",  
        temperature=0,  
    )  
    browser = create_sync_playwright_browser()  
    toolkits = PlayWrightBrowserToolkit.from_browser(sync_browser=browser)  
  
    tools = toolkits.get_tools()  
  
    agent_chain = initialize_agent(  
        agent=AgentType.STRUCTURED_CHAT_ZERO_SHOT_REACT_DESCRIPTION,  
        llm=llm,  
        tools=tools,  
        verbose=True  
    )  
  
    result = agent_chain.invoke(  
        input="Traga as ultimas noticias deste site https://g1.globo.com/ultimas-noticias/,")  
  
    print(result.get("output"))
    