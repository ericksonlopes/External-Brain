
#mcp #python #fastmcp 
## Aprenda a criar um servidor MCP utilizando Python e FastMCP.  
  
# Introdução  
  
No mundo da programação, a criação de servidores e APIs é uma habilidade essencial. Neste artigo, vamos explorar como construir um servidor MCP (Multi-Client Protocol) básico utilizando a biblioteca FastMCP em Python. MCP é um protocolo que permite a comunicação entre múltiplos clientes e um servidor. Vamos abordar os conceitos principais e fornecer exemplos práticos.  
  
## Pré-requisitos  
  
Antes de começarmos, certifique-se de ter o Python instalado em sua máquina. Você pode baixar a última versão do Python [aqui](https://www.python.org/downloads/). Além disso, instale as bibliotecas necessárias:  
  
```bash  
pip install fastmcp pydantic
```  
  
## Estrutura do Servidor MCP  
  
Abaixo, descrevemos a estrutura básica do nosso servidor MCP. Vamos começar importando as bibliotecas necessárias:  
  
```python  
import json  
import os  
import random  
import sys  
from datetime import datetime  
  
from mcp.server.fastmcp import FastMCP  
from pydantic import BaseModel, Field  
```    
Aqui estamos importando o `json` para manipulação de dados, `os` e `sys` para interações com o sistema, e `random` para gerar números aleatórios. O `datetime` nos ajudará a obter a data e hora atuais. A biblioteca `FastMCP` é o coração do nosso servidor, e `Pydantic` nos auxilia na validação de dados.  
  
### Configurando o Ambiente  
  
Para garantir que o servidor funcione corretamente no Windows, configuramos a codificação para UTF-8:  
  
```python  
if sys.platform == "win32":  
    os.environ["PYTHONIOENCODING"] = "utf-8"```    
### Criando o Servidor  
  
Agora, vamos criar uma instância do nosso servidor MCP:  
  
```python  
mcp = FastMCP("Servidor MCP Básico")  
```    
### Ferramentas do Servidor  
  
#### Obtendo a Data e Hora Atual  
  
Uma das ferramentas que vamos implementar é a capacidade de obter a data e hora atuais:  
  
```python  
@mcp.tool()  
def get_datetime() -> str:  
    """Retorna a data e hora atual"""    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")  
```    
#### Somando Dois Números  
  
Outra ferramenta simples é a função de soma:  
  
```python  
@mcp.tool()  
def somar(a: int, b: int) -> str:  
    """Soma dois números"""    resultado = a + b  
    return str(resultado)  
```    
### Estrutura de Dados com Pydantic  
  
Utilizaremos o Pydantic para definir a estrutura de dados de uma pessoa:  
  
```python  
class Person(BaseModel):  
    """Estrutura de Informação de Pessoa."""    name: str = Field(..., description="Nome da pessoa")  
    age: int = Field(..., description="Idade da pessoa")  
```    
Agora, vamos criar uma ferramenta que retorna informações sobre uma pessoa fictícia:  
  
```python  
@mcp.tool()  
def get_person(name: str) -> Person:  
    """Retorna informações sobre uma pessoa fictícia baseada no nome fornecido."""    random_age = random.randint(1, 100)  
    return Person(  
        name=f"Pessoa de {name}",  
        age=random_age  
    )```    
### Recursos Estáticos  
  
Podemos adicionar recursos estáticos ao nosso servidor. Aqui está como definir a configuração:  
  
```python  
@mcp.resource("config://servidor")  
def configuracao() -> str:  
    """Configuração do servidor"""    config = {        "nome": "Servidor MCP Básico",        "versao": "1.0",        "ativo": True    }    return json.dumps(config, indent=2, ensure_ascii=False)  
```    
### Executando o Servidor  
  
Por fim, para iniciar o servidor, utilizamos o seguinte bloco de código:  
  
```python  
if __name__ == "__main__":  
    print("Iniciando servidor MCP...")  
    mcp.run(transport="stdio")  
```    
## Boas Práticas  
- **Validação de Dados**: Sempre valide os dados de entrada para evitar erros e vulnerabilidades.  
- **Documentação**: Comente suas funções e mantenha a documentação atualizada.  
- **Teste**: Realize testes para garantir que suas ferramentas funcionem conforme esperado.  
  
## Conclusão  
  
Neste artigo, construímos um servidor MCP básico utilizando Python e FastMCP. Aprendemos a criar ferramentas, manipular dados e definir estruturas usando o Pydantic. Com essas habilidades, você pode expandir seu conhecimento em desenvolvimento de servidores e APIs. Experimente adicionar novas funcionalidades ao seu servidor e continue explorando!    
  
## Referências  
- [Python Official Documentation](https://docs.python.org/3/)  
- [FastMCP Documentation](https://fastmcp.readthedocs.io/en/latest/)  
- [Pydantic Documentation](https://pydantic-docs.helpmanual.io/)  
  
  
## Exemplos de Código  

```python  
import json  
import os  
import random  
import sys  
from datetime import datetime  
  
from mcp.server.fastmcp import FastMCP  
from pydantic import BaseModel, Field  

if sys.platform == "win32":  
    os.environ["PYTHONIOENCODING"] = "utf-8"
    
mcp = FastMCP("Servidor MCP Básico")  

@mcp.tool()  
def get_datetime() -> str:  
    """Retorna a data e hora atual"""    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")  

@mcp.tool()  
def somar(a: int, b: int) -> str:  
    """Soma dois números"""    resultado = a + b  
    return str(resultado)  

class Person(BaseModel):  
    """Estrutura de Informação de Pessoa."""    name: str = Field(..., description="Nome da pessoa")  
    age: int = Field(..., description="Idade da pessoa")  
@mcp.tool()  
def get_person(name: str) -> Person:  
    """Retorna informações sobre uma pessoa fictícia baseada no nome fornecido."""    random_age = random.randint(1, 100)  
    return Person(  
        name=f"Pessoa de {name}",  
        age=random_age  
    )

@mcp.resource("config://servidor")  
def configuracao() -> str:  
    """Configuração do servidor"""    config = {        "nome": "Servidor MCP Básico",        "versao": "1.0",        "ativo": True    }    return json.dumps(config, indent=2, ensure_ascii=False)  

if __name__ == "__main__":  
    print("Iniciando servidor MCP...")  
    mcp.run(transport="stdio")  
```  
  
## Referências  
  
- https://docs.python.org/3/  
- https://fastmcp.readthedocs.io/en/latest/  
- https://pydantic-docs.helpmanual.io/  
  
---  
*Este artigo foi criado para ajudar desenvolvedores a entenderem o básico sobre servidores MCP e a biblioteca FastMCP.*  
  
---  
#Python #FastMCP #Servidor #API #Pydantic