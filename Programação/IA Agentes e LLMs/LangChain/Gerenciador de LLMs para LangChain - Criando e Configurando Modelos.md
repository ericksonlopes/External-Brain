# 🏭 LLM Factory — Gerenciador de LLMs para LangChain

Padrão **Factory** para criar instâncias de LLMs do LangChain de forma configurável. Usa Pydantic para validação e permite adicionar novos provedores (OpenAI, Anthropic, etc.) sem alterar o código consumidor.

## 🔧 Dependências

```bash
pip install langchain-core langchain-openai pydantic
```

## 🏗️ Arquitetura

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  LLMConfig   │◀────│LLMConfigOpenai│     │  LLMPlatform │
│  (base)      │     │  (+api_key)  │     │  (enum)      │
└──────┬───────┘     └──────────────┘     └──────────────┘
       │ extends
┌──────▼───────┐     ┌──────────────┐
│  LLMFactory  │────▶│  ChatOpenAI  │  ← Runnable do LangChain
│  (get_llm)   │     │  (instância) │
└──────────────┘     └──────────────┘
```

## 📄 Implementação

### 1. Enums e Configuração

```python
from enum import Enum
from typing import Any, Dict

from langchain_core.runnables import Runnable
from langchain_openai import ChatOpenAI
from pydantic import BaseModel, Field, SecretStr


class LLMPlatform(str, Enum):
    OPENAI = "openai"


class LLMConfig(BaseModel):
    """Configuração base para qualquer LLM."""
    model: str = Field(description="Nome do modelo a ser utilizado.")
    platform: LLMPlatform | None = Field(default=None, description="Plataforma do modelo.")
    max_retries: int = Field(default=2, description="Tentativas em caso de falha.")
    temperature: float = Field(default=0.7, description="Temperatura de amostragem (criatividade).")


class LLMConfigOpenai(LLMConfig):
    """Configuração específica para modelos OpenAI."""
    api_key: SecretStr = Field(description="Chave da API OpenAI.")
```

### 2. Registro de Modelos

```python
models_config: Dict[str, LLMConfig] = {
    "gpt-4o-mini": LLMConfigOpenai(
        model="gpt-4o-mini",
        platform=LLMPlatform.OPENAI,
        api_key=SecretStr("your-openai-api-key-here"),
    ),
}
```

### 3. Factory

```python
class LLMFactory(LLMConfig):
    """Fábrica que cria instâncias de LLM com base na configuração registrada."""

    def __init__(self, /, **data: Any):
        super().__init__(**data)
        self._models_config: Dict[str, LLMConfig] = data.get("models_config", models_config)

    def get_llm(self) -> Runnable:
        model_conf = self._models_config.get(self.model)
        if not model_conf:
            raise ValueError(f"Modelo '{self.model}' não encontrado.")

        provider = getattr(model_conf, "platform", "").value if model_conf.platform else ""

        try:
            # Mescla config registrada com overrides do caller
            caller_values = self.model_dump(exclude_unset=True)
            for key in model_conf.model_dump(exclude_unset=True):
                caller_values.pop(key, None)
            merged_conf = model_conf.model_copy(update=caller_values)

            if provider == "openai":
                return self._create_openai_llm(merged_conf)
            else:
                raise ValueError(f"Provedor '{provider}' não suportado")

        except Exception as error:
            raise RuntimeError(
                f"Erro ao criar LLM (model={self.model}, provider={provider}): {error}"
            ) from error

    @classmethod
    def _create_openai_llm(cls, config: LLMConfigOpenai) -> Runnable:
        return ChatOpenAI(
            model=config.model,
            api_key=config.api_key,
            temperature=config.temperature,
            max_retries=config.max_retries,
        )
```

## 🚀 Uso

```python
# Usar modelo registrado com config padrão
llm = LLMFactory(model="gpt-4o-mini").get_llm()

# Override de temperatura para uma chamada específica
llm_criativo = LLMFactory(model="gpt-4o-mini", temperature=1.0).get_llm()

# Usar com LangChain
response = llm.invoke("Explique o padrão Factory em Python")
print(response.content)
```

## ⚙️ Como Funciona o Merge de Configurações

| Prioridade | Fonte | Exemplo |
|---|---|---|
| 1. Mais alta | Parâmetros passados no `LLMFactory(...)` | `temperature=1.0` |
| 2. Base | Valores registrados em `models_config` | `api_key`, `platform` |
| 3. Mais baixa | Defaults do `LLMConfig` | `max_retries=2` |

## 📝 Notas

- Para adicionar um novo provedor (ex: Anthropic), crie `LLMConfigAnthropic`, registre em `models_config` e adicione um `elif` no `get_llm`.
- A `api_key` usa `SecretStr` do Pydantic — nunca é exposta em logs ou `repr()`.
- `LLMFactory` herda de `LLMConfig`, então aceita os mesmos campos como overrides.

---
#python #langchain #llm #pydantic #openai #factory-pattern