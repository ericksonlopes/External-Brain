#python #langchain #chain #langchain_community #langchain_openai #openai 

```bash
pip install langchain[openai] langchain-community
```

```python 
import os  
from operator import itemgetter  
  
from langchain_core.output_parsers import StrOutputParser  
from langchain_core.prompts import PromptTemplate  
from langchain_core.runnables import RunnablePassthrough  
from langchain_openai import OpenAI  
  
os.environ["OPENAI_API_KEY"] = ""  
  
llm = OpenAI(temperature=0.7)  
  
# Chain 1: Gerar nome da empresa  
# Chains individuais  
prompt_one = PromptTemplate.from_template(  
    "Sugira um nome para uma empresa que fabrica {produto}."  
)  
chain_one = prompt_one | llm | StrOutputParser()  
  
prompt_two = PromptTemplate.from_template(  
    "Crie um slogan para {nome_empresa}."  
)  
chain_two = prompt_two | llm | StrOutputParser()  
  
prompt_three = PromptTemplate.from_template(  
    "Crie uma descrição de 50 palavras para {nome_empresa} com slogan: {slogan}"  
)  
chain_three = prompt_three | llm | StrOutputParser()  
  
# Sequential chain moderno que mantém todas as saídas intermediárias  
sequential_chain = (  
        {"produto": itemgetter("produto")}  
        | RunnablePassthrough.assign(nome_empresa=chain_one)  
        | RunnablePassthrough.assign(slogan=chain_two)  
        | RunnablePassthrough.assign(descricao=chain_three)  
)  
  
# Executando  
resultado = sequential_chain.invoke({"produto": "detergente ecológico"})  
print(resultado)
```