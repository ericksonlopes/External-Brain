# 🤖 Iniciando com CrewAI: Orquestração de Agentes

O **CrewAI** permite criar sistemas onde múltiplos agentes de IA colaboram para resolver tarefas complexas, simulando uma equipe humana com papéis e objetivos distintos.

## 🛠️ Instalação

```bash
pip install crewai langchain-openai loguru
```

## 📄 Exemplo: Agente Especialista em Python

Este script configura um agente, define uma tarefa de explicação técnica e executa a "equipe" (Crew).

```python
from crewai import Agent, Task, Crew
from langchain_openai import ChatOpenAI

# 1. Configuração do Modelo (LLM)
llm = ChatOpenAI(model="gpt-3.5-turbo", temperature=0.5)

# 2. Criação do Agente
python_expert = Agent(
    role="Especialista em Python",
    goal="Ajudar iniciantes com dúvidas de programação",
    backstory="Você é um desenvolvedor sênior com 20 anos de experiência.",
    llm=llm,
    verbose=True
)

# 3. Definição da Tarefa
task = Task(
    description="Explique o que são Decorators em Python com exemplos práticos.",
    expected_output="Uma explicação didática e código funcional.",
    agent=python_expert
)

# 4. Orquestração da Equipe
crew = Crew(
    agents=[python_expert],
    tasks=[task],
    verbose=True
)

# Início do processo
result = crew.kickoff()
print(result)
```

## 🎯 Componentes Chave

- **Agent**: O "colaborador" com personalidade e metas.
- **Task**: O trabalho específico a ser feito.
- **Crew**: O gerente que une agentes e tarefas em um fluxo de trabalho.

---
#ia  #crewai #agents #python #llm #openai
