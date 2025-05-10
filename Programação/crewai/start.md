# Explicação do Código - `main.py`

O código apresentado utiliza bibliotecas para criar um agente de IA especializado em Python, que responde a dúvidas sobre programação. Abaixo está a explicação de cada parte:

## 1. **Importação de Bibliotecas**
```python
from crewai import Agent, Task, Crew
from langchain_openai import ChatOpenAI
from config import Config
```
- Importa classes e funções necessárias para configurar o agente, tarefas e equipe.
- A classe `Config` é usada para carregar variáveis de ambiente.

---

## 2. **Configuração Inicial**
```python
Config()
```
- Inicializa a classe `Config`, que carrega a chave da API OpenAI do arquivo `.env`.

---

## 3. **Definição do Modelo de Linguagem**
```python
llm = ChatOpenAI(model="gpt-3.5-turbo", temperature=0.5)
```
- Configura o modelo de linguagem GPT-3.5-turbo com uma temperatura de 0.5 (controla a criatividade das respostas).

---

## 4. **Criação do Agente**
```python
python_agent = Agent(
    role="Especialista em Python",
    goal="Ajudar com dúvidas sobre programação em Python",
    backstory="Você é um programador experiente que ajuda iniciantes a resolver problemas.",
    verbose=True,
    llm=llm,
)
```
- Cria um agente com o papel de "Especialista em Python".
- Define o objetivo, contexto e modelo de linguagem usado pelo agente.

---

## 5. **Definição da Tarefa**
```python
task = Task(
    description="Explique o que são listas em Python e como usá-las com exemplos.",
    expected_output="Uma explicação clara e exemplos de uso de listas em Python.",
    agent=python_agent,
)
```
- Define uma tarefa para o agente, com uma descrição e o resultado esperado.

---

## 6. **Criação da Equipe**
```python
crew = Crew(
    agents=[python_agent],
    tasks=[task],
    verbose=True
)
```
- Cria uma equipe (`Crew`) que associa o agente à tarefa definida.

---

## 7. **Execução da Tarefa**
```python
result = crew.kickoff()
print(result)
```
- Executa a tarefa com o método `kickoff` e exibe o resultado no console.

---

## Resumo
O código configura um agente de IA que utiliza o modelo GPT-3.5-turbo para responder a perguntas sobre Python. Ele define uma tarefa específica, associa o agente a essa tarefa e executa-a, exibindo o resultado.