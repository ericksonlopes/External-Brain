
#client #mcp #fastmcp #python

Este tutorial explica como criar um **cliente MCP** que se conecta a um servidor usando o protocolo MCP via stdio, interage com as ferramentas (tools), prompts e recursos do servidor, e exibe os resultados no terminal. O exemplo utiliza asyncio e a biblioteca MCP Client.

***

## Visão Geral

O código demonstra:

- Como configurar os parâmetros de conexão para um servidor MCP iniciado via Python (no exemplo, o script `server_side.py`)
- Como inicializar uma sessão MCP assíncrona
- Como listar e chamar tools, prompts e resources no servidor
- Como manipular e imprimir os resultados dessas operações

***

## Estrutura do Código

### Imports Essenciais

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client
```

Esses módulos fornecem as classes e funções para gerenciar a comunicação cliente-servidor via stdin/stdout.

***

### Função Principal Assíncrona

```python
async def main():
    server_params = StdioServerParameters(
        command="python",
        args=["server_side.py"]
    )
```

- **StdioServerParameters:** Define como o servidor será chamado localmente (comando, argumentos e variáveis de ambiente).
- **Obs.:** `server_side.py` deve ser substituído pelo nome do script do seu servidor MCP.


#### Estabelecendo a Conexão

```python
    async with stdio_client(server_params) as (read, write):
        async with ClientSession(read, write) as session:
            print("🔌 Conectando ao servidor MCP...")
            # Inicializar sessão
            await session.initialize()
            print("✅ Conexão estabelecida!")
```

- Inicia o servidor como subprocesso.
- Abre uma sessão cliente para comunicação.
- Inicializa a conexão assíncrona.


#### Exemplos de Interações

**Listar Tools Disponíveis**

```python
            list_tools = await session.list_tools()
            print(f"\n🛠️ Tools disponíveis: {[tool.name for tool in list_tools.tools]}")
```

Chama o método para listar as ferramentas disponíveis no servidor MCP.

**Chamar Tool para Data e Hora**

```python
            datetime = await session.call_tool("get_datetime", arguments={})
            print(f"\n📅 Data atual: {datetime.content[0].text}")
```

Chama a tool `get_datetime` e imprime a data/hora retornada.

**Chamar Tool para Soma**

```python
            result_somar = await session.call_tool("somar", arguments={"a": 15, "b": 27})
            print(f"Resultado: 15 + 27 = {result_somar.content.text}")
```

Chama a tool `somar` passando parâmetros `a` e `b`.

**Chamar Tool para Gerar Pessoa**

```python
            person = await session.call_tool("get_person", arguments={"name": "Erick"})
            print(f"\n👤 Pessoa: {person.structuredContent}")
```

Chama a tool `get_person`, demonstrando como receber objetos estruturados (ex.: JSON/dict).

**Listar Prompts Disponíveis**

```python
            list_prompt = await session.list_prompts()
            print(f"\n📜 Prompts disponíveis: {[prompt.name for prompt in list_prompt.prompts]}")
```

Lista os templates (‘prompts’) do servidor.

**Usar Prompt para Revisão de Código**

```python
            revisar_codigo = await session.get_prompt("revisar_codigo", arguments={
                "codigo": "def hello_world():\n    print('Hello, world!')",
                "linguagem": "python"
            })
            print(f"\n🔍 Revisão de código:\n{revisar_codigo}")
```

Chama o prompt `revisar_codigo` e exibe o resultado.

**Listar Recursos Disponíveis**

```python
            list_resources = await session.list_resources()
            print(f"\n📂 Recursos disponíveis: {[resource.name for resource in list_resources.resources]}")
```

Lista os recursos (resources) definidos do servidor.

**Ler Configuração**

```python
            configuracao = await session.read_resource("config://settings")
            print(f"\n⚙️ Configuração do servidor:\n{configuracao}")
```

Acessa um recurso específico do servidor e imprime seu conteúdo.

***

### Execução do Script

```python
if __name__ == '__main__':
    import asyncio
    asyncio.run(main())
```

Garante que a função assíncrona **main()** seja executada caso o script rode diretamente.

***

## Como Usar

### Pré-requisitos

1. **Dependências**:

```bash
pip install mcp
```

2. **Servidor MCP**: O script `server_side.py` (ou equivalente) deve estar disponível e implementando o protocolo MCP, como no exemplo do tutorial anterior.

### Executando o Cliente

1. Salve o código acima em um arquivo, ex: `cliente_mcp.py`.
2. No terminal, execute:

```bash
python cliente_mcp.py
```

3. O cliente inicializará o servidor e começará a imprimir no terminal as informações de conexão, tools, prompts, resources e resultados das chamadas feitas.

***

## Dicas e Customizações

- **Token**: Substitua `"seu_token_aqui"` por um valor real, se a autenticação do servidor MCP exigir.
- **Argumentos**: Modifique a lista de `args` conforme o nome e localização do seu servidor MCP.
- **Adicione novas interações**: Consulte as docs da sua biblioteca MCP para mais comandos (ex.: envio de recursos, manipulação de sessões avançadas, etc).
- **Asyncio**: Todo fluxo é completamente assíncrono, ideal para aplicações reativas ou processamento em lote.

***

## Troubleshooting

- **Erro de import**: Confirme se o pacote `mcp` está instalado corretamente.
- **Incompatibilidade de nome de tools/resources/prompts**: Os nomes chamados no cliente devem existir exatamente como definidos no servidor MCP.
- **Permissões e variáveis**: Se necessário, ajuste variáveis de ambiente e permissões do sistema operacional para rodar subprocessos.

***

## Considerações Finais

Esse cliente demonstra de forma clara e didática como interagir com servidores MCP para explorar todas as ferramentas, prompts e recursos disponíveis para automação, integração com IA e muito mais. Expanda e adapte conforme sua necessidade!

Se precisar de exemplos de integração mais avançados ou dúvidas sobre o protocolo, consulte a documentação oficial da biblioteca MCP.

