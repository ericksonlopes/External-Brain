#langchain #chain #python #openai #langchain_community #langc

```bash
pip install langchain[openai] langchain-community
```

```python
import os  
  
from langchain_core.output_parsers import StrOutputParser  
from langchain_core.prompts import PromptTemplate  
from langchain_openai import OpenAI  
  
os.environ["OPENAI_API_KEY"] = ""  
  
llm = OpenAI(temperature=0.7)  
  
# Chain 1: Gerar nome da empresa  
prompt_one = PromptTemplate.from_template(  
    "Sugira um nome para uma empresa que fabrica {produto}."  
)  
chain_one = prompt_one | llm | StrOutputParser()  
  
# Chain 2: Gerar slogan  
prompt_two = PromptTemplate.from_template(  
    "Crie um slogan cativante para a empresa: {company_name}"  
)  
chain_two = prompt_two | llm | StrOutputParser()  
  
# Criando o chain sequencial moderno  
simple_chain = (  
        {"company_name": chain_one}  
        | chain_two  
)  
  
# Executando  
output = simple_chain.invoke({"produto": "Carros de corrida"})  
print(output)

```