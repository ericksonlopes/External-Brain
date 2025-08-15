

#mcp #python #fastmcp 

Este tutorial explica como criar um servidor MCP (Model Context Protocol) básico usando a biblioteca FastMCP em Python. O código demonstra a implementação de tools, prompts e recursos para criar um servidor funcional.

## Visão Geral

O código implementa um servidor MCP que fornece:
- **Tools**: Funções que podem ser chamadas pelo cliente
- **Prompts**: Templates para geração de conteúdo
- **Resources**: Recursos estáticos ou dinâmicos acessíveis pelo cliente

## Estrutura do Código

### Imports e Configuração Inicial

```python
import json
import os
import random
import sys
from datetime import datetime

from mcp.server.fastmcp import FastMCP
from pydantic import BaseModel, Field
```

**Propósito**: Importa as bibliotecas necessárias:
- Bibliotecas padrão do Python para manipulação de dados
- FastMCP para criar o servidor
- Pydantic para validação de dados

### Configuração de Codificação

```python
if sys.platform == "win32":
    os.environ["PYTHONIOENCODING"] = "utf-8"
```

**Propósito**: Garante que caracteres UTF-8 sejam exibidos corretamente no Windows.

### Criação do Servidor

```python
mcp = FastMCP("Servidor MCP Básico")
```

**Propósito**: Cria uma instância do servidor MCP com o nome "Servidor MCP Básico".

## Implementação de Tools

### Tool 1: Obter Data e Hora

```python
@mcp.tool()
def get_datetime() -> str:
    """Retorna a data e hora atual"""
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")
```

**Funcionalidade**:
- Retorna a data e hora atual formatada
- Útil para operações que precisam de timestamp

### Tool 2: Somar Números

```python
@mcp.tool()
def somar(a: int, b: int) -> str:
    """Soma dois números"""
    resultado = a + b
    return str(resultado)
```

**Funcionalidade**:
- Recebe dois números inteiros
- Retorna a soma como string
- Demonstra como criar tools com parâmetros

### Tool 3: Gerar Pessoa Fictícia

```python
class Person(BaseModel):
    """Weather information structure."""
    name: str = Field(..., description="Nome da pessoa")
    age: int = Field(..., description="Idade da pessoa")

@mcp.tool()
def get_person(name: str) -> Person:
    """Retorna informações sobre uma pessoa fictícia baseada na cidade fornecida."""
    random_age = random.randint(1, 100)
    
    return Person(
        name=f"Pessoa de {name}",
        age=random_age
    )
```

**Funcionalidade**:
- Define um modelo Pydantic para estruturar dados
- Gera uma pessoa fictícia com idade aleatória
- Demonstra como retornar objetos estruturados

## Implementação de Prompts

### Prompt para Revisão de Código

```python
@mcp.prompt()
def revisar_codigo(codigo: str, linguagem: str = "python") -> str:
    """Template para revisão de código"""
    return f"""Por favor, revise este código {linguagem}:

{codigo}

Forneça sugestões de melhoria, identifique possíveis bugs e
comente sobre as melhores práticas.
"""
```

**Funcionalidade**:
- Cria um template para revisão de código
- Aceita o código e a linguagem como parâmetros
- Retorna um prompt formatado para análise

## Implementação de Resources

### Resource 1: Configuração do Servidor

```python
@mcp.resource("config://servidor")
def configuracao() -> str:
    """Configuração do servidor"""
    config = {
        "nome": "Servidor MCP Básico",
        "versao": "1.0",
        "ativo": True
    }
    return json.dumps(config, indent=2, ensure_ascii=False)
```

**Funcionalidade**:
- Fornece configurações do servidor em formato JSON
- Utiliza URI `config://servidor` para identificação

### Resource 2: Leitura de Documentos

```python
@mcp.resource("file://documents/{name}")
def read_document(name: str) -> str:
    """Read a document by name."""
    return f"Content of {name}"
```

**Funcionalidade**:
- Resource dinâmico que aceita parâmetros na URI
- Simula leitura de documentos por nome

### Resource 3: Configurações da Aplicação

```python
@mcp.resource("config://settings")
def get_settings() -> str:
    """Get application settings."""
    return """{
  "theme": "dark",
  "language": "pt",
  "debug": false
}"""
```

**Funcionalidade**:
- Retorna configurações da aplicação em JSON
- Exemplo de resource estático

## Execução do Servidor

### Inicialização

```python
if __name__ == "__main__":
    print("Iniciando servidor MCP...")
    mcp.run(transport="stdio")
```

**Funcionalidade**:
- Verifica se o script está sendo executado diretamente
- Inicia o servidor usando transporte stdio (entrada/saída padrão)

## Como Usar

### Pré-requisitos

1. Instale as dependências:
```bash
pip install fastmcp pydantic
```

### Executando o Servidor

1. Salve o código em um arquivo (ex: `servidor_mcp.py`)
2. Execute o servidor:
```bash
python servidor_mcp.py
```

### Testando as Funcionalidades

#### Tools Disponíveis:
- `get_datetime()`: Obtém data/hora atual
- `somar(a, b)`: Soma dois números
- `get_person(name)`: Gera pessoa fictícia

#### Prompts Disponíveis:
- `revisar_codigo(codigo, linguagem)`: Template para revisão de código

#### Resources Disponíveis:
- `config://servidor`: Configuração do servidor
- `file://documents/{name}`: Leitura de documentos
- `config://settings`: Configurações da aplicação

## Conceitos Importantes

### Decorators MCP

- `@mcp.tool()`: Define uma função como tool executável
- `@mcp.prompt()`: Define um template de prompt
- `@mcp.resource(uri)`: Define um resource acessível via URI

### Tipos de Retorno

- **Tools**: Podem retornar strings, objetos Pydantic ou tipos básicos
- **Prompts**: Sempre retornam strings (templates)
- **Resources**: Sempre retornam strings (normalmente JSON)

### URIs de Resources

- `config://`: Para configurações
- `file://`: Para arquivos
- Parâmetros dinâmicos: `{name}` na URI

## Extensões Possíveis

### Adicionar Mais Tools

```python
@mcp.tool()
def calcular_imc(peso: float, altura: float) -> str:
    """Calcula o IMC (Índice de Massa Corporal)"""
    imc = peso / (altura ** 2)
    return f"IMC: {imc:.2f}"
```

### Adicionar Resources Complexos

```python
@mcp.resource("database://users/{id}")
def get_user(id: str) -> str:
    """Simula busca de usuário no banco de dados"""
    # Aqui você conectaria com um banco real
    user_data = {"id": id, "name": "Usuario", "email": "user@email.com"}
    return json.dumps(user_data)
```

### Tratamento de Erros

```python
@mcp.tool()
def dividir(a: float, b: float) -> str:
    """Divide dois números com tratamento de erro"""
    if b == 0:
        return "Erro: Divisão por zero não é permitida"
    resultado = a / b
    return str(resultado)
```

## Considerações de Segurança

1. **Validação de Entrada**: Use Pydantic para validar parâmetros
2. **Sanitização**: Sempre valide dados externos
3. **Permissões**: Implemente controle de acesso adequado
4. **Logs**: Adicione logging para monitoramento

## Conclusão

Este servidor MCP básico demonstra os conceitos fundamentais para criar aplicações que interagem com modelos de linguagem. O FastMCP simplifica significativamente o desenvolvimento, permitindo focar na lógica de negócio em vez da infraestrutura de comunicação.

Com essas bases, você pode expandir o servidor adicionando mais tools, integrando com bancos de dados, APIs externas ou qualquer funcionalidade que sua aplicação necessite.