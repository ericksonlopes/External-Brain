
  ## Uma abordagem prática para desenvolvedores Python intermediários  
  
# Introdução  
  
Nos últimos anos, a inteligência artificial tem se tornado uma ferramenta poderosa em diversas aplicações. Neste artigo, vamos explorar como conectar um agente de IA utilizando o Multi-Command Protocol (MCP) com a biblioteca CrewAI. Este guia é voltado para desenvolvedores Python intermediários que desejam integrar suas soluções de IA com uma interface de comando eficiente.  
  
# O que é MCP?  
  
O Multi-Command Protocol (MCP) é um protocolo que permite a comunicação entre agentes de IA e servidores de maneira estruturada. Ele facilita a troca de comandos e respostas, permitindo que os agentes realizem tarefas específicas de forma mais eficiente.  
  
# Preparação do Ambiente  
  
Antes de começarmos, certifique-se de ter o Python instalado em sua máquina e siga os passos abaixo para configurar o ambiente:  
  
1. **Instalação das Dependências**: Execute o seguinte comando para instalar as bibliotecas necessárias:  
     
```bash  
pip install crewai "crewai-tools[mcp]"   
```

2. **Defina sua chave de API**: Você precisará de uma chave de API da OpenAI. Armazene-a em uma variável de ambiente chamada `OPENAI_API_KEY`.  
  
# Conectando o Agente de IA  
  
Agora que temos o ambiente configurado, vamos ao código! Abaixo está um exemplo de como conectar seu agente de IA via MCP utilizando a biblioteca CrewAI:  

```python  
from crewai import Agent, Task, Crew
from crewai_tools import MCPServerAdapter
from mcp import StdioServerParameters
import os

os.environ["OPENAI_API_KEY"] = "SUA_CHAVE_API_OPENAI"

# Configuração dos parâmetros do servidor MCP  
server_params = StdioServerParameters(
    command="npx",
    args=["-y", "@openbnb/mcp-server-airbnb", "--ignore-robots-txt"],
)

with MCPServerAdapter(server_params) as tools:
    # Criando um agente para interagir com o Airbnb    
    agent = Agent(
        role="Especialista em Busca do Airbnb",
        goal="Buscar e analisar informações de acomodações no Airbnb",
        backstory="Um especialista em encontrar as melhores opções de acomodações no Airbnb",
        tools=tools,
        verbose=True,
    )
    # Criando uma tarefa para o agente    
    task = Task(
        description="Buscar informações de acomodações no Airbnb no capão dedondo com preço máximo de 100 reais por noite",
        expected_output="Lista de acomodações disponíveis com detalhes",
        agent=agent,
    )
    # Criando e executando a crew    
    crew = Crew(
        agents=[agent],
        tasks=[task],
        verbose=True,
    )

result = crew.kickoff()
print(result)  
```  
  
## Explicação do Código  
  
1. **Importação das Bibliotecas**: Começamos importando as classes necessárias da biblioteca `crewai` e `crewai_tools`. Também importamos `StdioServerParameters` para configurar o servidor MCP.  
  
2. **Configuração da Chave API**: A chave da API da OpenAI deve ser definida como uma variável de ambiente para permitir que o agente acesse a API.  
  
3. **Configuração do Servidor MCP**: Utilizamos a classe `StdioServerParameters` para definir o comando e os argumentos necessários para executar o servidor MCP.  
  
4. **Criação do Agente**: Um agente é criado com um papel específico, um objetivo e uma história de fundo. Os `tools` fornecem as funcionalidades necessárias para o agente.  
  
5. **Definição de Tarefas**: Uma tarefa é criada, onde descrevemos o que o agente deve buscar, incluindo uma descrição e um resultado esperado.  
  
6. **Execução da Crew**: Por fim, criamos uma `Crew` que contém o agente e a tarefa e chamamos o método `kickoff()` para iniciar a execução. O resultado é impresso na tela.  
  
# Boas Práticas  
- **Gerenciamento de Erros**: Sempre implemente um tratamento de erros para lidar com falhas na conexão ou na execução do agente.  
- **Modularidade**: Separe seu código em funções ou classes para melhorar a legibilidade e manutenção.  
- **Documentação**: Comente seu código e mantenha uma documentação clara sobre como as partes interagem entre si.  
  
# Conclusão  
  
Neste artigo, vimos como conectar um agente de IA utilizando o Multi-Command Protocol com a biblioteca CrewAI. A implementação de agentes de IA pode ser complexa, mas com o uso de protocolos como o MCP, conseguimos simplificar a comunicação e as interações. Agora, você pode expandir esse conceito e explorar outras funcionalidades que a CrewAI oferece!  
  
# Referências  
- [Documentação do CrewAI](https://crewai.com/docs)  
- [Multi-Command Protocol](https://mcp-protocol.org)  
  
# Nota do Autor  
Se você tiver alguma dúvida ou sugestão, sinta-se à vontade para entrar em contato. Estou sempre aberto a discutir sobre IA e desenvolvimento!  
  

## Referências  
  
- https://crewai.com/docs  
- https://mcp-protocol.org  
  
---  
*Se você tiver alguma dúvida ou sugestão, sinta-se à vontade para entrar em contato. Estou sempre aberto a discutir sobre IA e desenvolvimento!*  
  
---  
#Python #agents #MCP #CrewAI #crewai-tools