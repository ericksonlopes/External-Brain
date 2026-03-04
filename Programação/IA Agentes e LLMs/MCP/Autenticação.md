# 🔐 Autenticação no MCP (Model Context Protocol)

O **Model Context Protocol (MCP)** permite que LLMs interajam de forma segura com recursos e ferramentas locais ou remotas. A autenticação é um passo crítico para garantir que apenas agentes autorizados acessem os dados.

## 🛠️ Tipos de Autenticação Comuns

### 1. Variáveis de Ambiente (API Keys)
A forma mais comum de autenticar servidores MCP (como os da OpenAI, Anthropic ou bancos de dados) é através de chaves de API passadas via ambiente:

```json
{
  "mcpServers": {
    "meu-servidor": {
      "command": "python",
      "args": ["server.py"],
      "env": {
        "API_KEY": "sua-chave-secreta-aqui"
      }
    }
  }
}
```

### 2. Autenticação Local via SSH/Auth Tokens
Para servidores rodando em infraestrutura própria, pode-se usar tokens de portador (Bearer Tokens) ou certificados locais.

## 📄 Estrutura de Configuração (Claude Desktop)

No arquivo `claude_desktop_config.json`, a autenticação deve ser definida na seção `env` de cada servidor:

1.  Localize o arquivo em: `%APPDATA%\Claude\claude_desktop_config.json`
2.  Adicione as credenciais necessárias.

## 🔒 Boas Práticas de Segurança

- **Nunca** versionar arquivos de configuração que contenham senhas ou chaves.
- Use ferramentas como o `python-dotenv` para carregar chaves localmente durante o desenvolvimento.
- Revogue chaves que possam ter sido expostas acidentalmente em logs.

---
#ia  #mcp #security #autenticacao #llm
