

## Aprenda a personalizar a interação do seu agente de IA com ferramentas simples.

# Introdução

No mundo em constante evolução da inteligência artificial, criar agentes personalizados que possam interagir de forma eficaz com os usuários é uma tarefa cada vez mais relevante. Neste artigo, vamos explorar como criar ferramentas (tools) para seu agente de IA utilizando a biblioteca CrewAI. Veremos que, com alguns passos simples, é possível adicionar funcionalidades personalizadas ao seu agente.

# O que é o CrewAI?

CrewAI é uma biblioteca que facilita a criação e gestão de agentes de IA. Com ela, você pode definir o papel do agente, suas responsabilidades e até mesmo construir ferramentas que o agente pode usar para interagir com os usuários de maneira mais eficaz.

# Passo a Passo para Criar uma Ferramenta

## Passo 1: Instalação da Biblioteca

Primeiramente, precisamos instalar a biblioteca CrewAI. Isso pode ser feito facilmente utilizando o pip:

```bash
pip install crewai
```

## Passo 2: Criar uma Ferramenta Personalizada

Vamos começar criando uma ferramenta simples que retornará uma saudação personalizada. Para isso, utilizaremos o decorador `@tool` fornecido pela biblioteca CrewAI:

```python
from crewai.tools import tool

@tool
def saudacao(nome: str) -> str:
    """Retorna uma saudação personalizada."""
    return f"Olá, {nome}! Bem-vindo à CrewAI!"
```

Neste exemplo, a função `saudacao` recebe um nome como parâmetro e retorna uma saudação formatada.

## Passo 3: Criar um Agente

Agora que temos nossa ferramenta, podemos criar um agente que utilizará essa ferramenta. O agente pode ser definido da seguinte maneira:

```python
from crewai import Agent

meu_agente = Agent(
    role="Agente de Boas-Vindas",
    goal="Cumprimentar o usuário de forma personalizada",
    backstory="Este agente adora receber novos usuários e deixá-los confortáveis.",
    tools=[saudacao]
)
```

### Explicação dos Parâmetros
- `role`: Define o papel do agente. Neste caso, é um agente de boas-vindas.
- `goal`: O objetivo do agente. Aqui, o agente tem a tarefa de cumprimentar o usuário.
- `backstory`: Um pequeno contexto sobre a personalidade do agente, que pode ajudar na interação.
- `tools`: A lista de ferramentas que o agente pode utilizar, incluindo a nossa função `saudacao`.

## Passo 4: Criar uma Tarefa

Em seguida, vamos criar uma tarefa que o agente deve realizar. Essa tarefa descreverá o que o agente deve fazer:

```python
from crewai import Task

tarefa_cumprimentar = Task(
    description="Cumprimente o usuário chamado João utilizando sua ferramenta.",
    expected_output="Uma saudação personalizada para João.",
    agent=meu_agente
)
```

A tarefa inclui uma descrição e a saída esperada, que neste caso é a saudação personalizada para o usuário chamado João.

## Passo 5: Criar e Executar a Crew

Finalmente, podemos criar uma equipe (Crew) que inclui nosso agente e a tarefa que ele deve realizar:

```python
from crewai import Crew

equipe = Crew(
    agents=[meu_agente],
    tasks=[tarefa_cumprimentar]
)
```

Agora, vamos executar a equipe com o input necessário:

```python
resultado = equipe.kickoff(inputs={"nome": "João"})
print(resultado)
```

Isso resultará na execução da tarefa e, como saída, teremos a saudação personalizada para João.

# Conclusão

Neste artigo, mostramos como é simples criar uma ferramenta personalizada para um agente de IA utilizando a biblioteca CrewAI. Com apenas algumas linhas de código, você pode aumentar a interatividade e a personalização do seu agente, tornando a experiência do usuário muito mais agradável.

# Boas Práticas
- Sempre documente suas ferramentas e agentes para facilitar o entendimento futuro.
- Mantenha suas funções de ferramenta simples e focadas em uma única responsabilidade.
- Teste suas implementações para garantir que o agente se comporte conforme o esperado.

# Dicas
- Explore outras funcionalidades da biblioteca CrewAI para expandir as capacidades do seu agente.
- Considere criar múltiplas ferramentas e atribuí-las a diferentes agentes para interações mais ricas.

Esses passos simples podem levar você a criar agentes de IA altamente eficazes e personalizados utilizando o CrewAI!

## Exemplos de Código

### Exemplo 1
```python
pip install crewai
```

### Exemplo 2
```python
from crewai import Agent, Task, Crew
from crewai.tools import tool


@tool
def saudacao(nome: str) -> str:
    """Retorna uma saudação personalizada."""
    return f"Olá, {nome}! Bem-vindo à CrewAI!"


meu_agente = Agent(
    role="Agente de Boas-Vindas",
    goal="Cumprimentar o usuário de forma personalizada",
    backstory="Este agente adora receber novos usuários e deixá-los confortáveis.",
    tools=[saudacao]
)

tarefa_cumprimentar = Task(
    description="Cumprimente o usuário chamado João utilizando sua ferramenta.",
    expected_output="Uma saudação personalizada para João.",
    agent=meu_agente
)

equipe = Crew(
    agents=[meu_agente],
    tasks=[tarefa_cumprimentar]
)

resultado = equipe.kickoff(inputs={"nome": "João"})
print(resultado)
```


*Sinta-se à vontade para experimentar o CrewAI e personalizar seus agentes da maneira que preferir!*

---
#IA #CrewAI #Agents #Python 