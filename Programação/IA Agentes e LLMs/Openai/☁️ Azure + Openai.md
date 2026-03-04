# ☁️ Azure OpenAI — Chat Completions com Python

Como usar a API da **OpenAI hospedada na Azure** para gerar respostas via chat completions.

## 🔧 Dependências

```bash
pip install openai
```

## ⚙️ Configuração

| Variável | Descrição |
|---|---|
| `azure_endpoint` | URL do recurso Azure (ex: `https://meu-recurso.openai.azure.com/`) |
| `api_key` | Chave de acesso do recurso Azure |
| `api_version` | Versão da API (ex: `2024-02-01`) |
| `model` | Nome do **deployment** criado no Azure (não o nome do modelo original) |

## 📄 Implementação

```python
from openai import AzureOpenAI

client = AzureOpenAI(
    azure_endpoint="https://meu-recurso.openai.azure.com/",
    api_key="sua-api-key",
    api_version="2024-02-01",
)

messages = [
    {"role": "system", "content": "You are an AI assistant that helps people find information."},
    {"role": "user", "content": "Qual a melhor forma de treinar um cachorro?"},
]

completion = client.chat.completions.create(
    model="gpt-35-turbo",  # nome do deployment no Azure
    messages=messages,
    max_tokens=1000,
    temperature=0.05,
)

print(completion.choices[0].message.content)
```

## 🔑 Parâmetros do `create()`

| Parâmetro | Tipo | Descrição |
|---|---|---|
| `model` | `str` | Nome do deployment no Azure |
| `messages` | `list[dict]` | Histórico de conversa (`system`, `user`, `assistant`) |
| `max_tokens` | `int` | Limite máximo de tokens na resposta |
| `temperature` | `float` | Criatividade (0.0 = determinístico, 1.0 = criativo) |
| `frequency_penalty` | `float` | Penaliza repetição de tokens já usados (0.0 a 2.0) |
| `presence_penalty` | `float` | Incentiva introdução de novos tópicos (0.0 a 2.0) |
| `n` | `int` | Número de respostas alternativas a gerar |
| `stop` | `list[str]` | Sequências que interrompem a geração |

## 💡 Diferença: Azure vs OpenAI Direto

| Característica | OpenAI Direto | Azure OpenAI |
|---|---|---|
| Client | `OpenAI(api_key=...)` | `AzureOpenAI(azure_endpoint=..., api_key=..., api_version=...)` |
| Modelo | Nome oficial (`gpt-4o`) | Nome do **deployment** criado no Azure |
| Billing | Conta OpenAI | Assinatura Azure |
| Compliance | Padrão | Enterprise (SOC2, HIPAA, etc.) |

## 📝 Notas

- O `model` no Azure é o nome do **deployment**, não o nome do modelo. Crie o deployment no Azure AI Studio.
- A `api_version` muda frequentemente — consulte a [documentação oficial](https://learn.microsoft.com/en-us/azure/ai-services/openai/reference).
- Para integrar com LangChain, use `ChatAzureOpenAI` em vez de `ChatOpenAI`.

---
#python #azure #openai #llm #ia