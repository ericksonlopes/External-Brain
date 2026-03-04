
Gerenciamento de configurações usando `pydantic-settings`. Carrega variáveis automaticamente do ambiente e de arquivos `.env` com validação de tipos.

## 🔧 Dependências

```bash
pip install pydantic-settings python-dotenv
```

## 📄 Implementação

```python
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    OPENAI_API_KEY: str

    class Config:
        env_file = ".env"


settings = Settings()
```

## 📁 Arquivo `.env`

```env
OPENAI_API_KEY=sk-abc123...
```

## 🚀 Exemplo Avançado

```python
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    # Obrigatórias (sem valor padrão — erro se não definidas)
    DATABASE_URL: str
    API_KEY: str

    # Opcionais (com valor padrão)
    DEBUG: bool = False
    PORT: int = 8000
    ENV: str = "production"

    class Config:
        env_file = ".env"
        env_file_encoding = "utf-8"


settings = Settings()
print(settings.DATABASE_URL)
print(settings.DEBUG)  # False (padrão) ou True (se definido no .env/ambiente)
```

## ⚙️ Ordem de Prioridade

O `BaseSettings` carrega valores na seguinte ordem (a mais alta sobrescreve):

1. **Variáveis de ambiente** do sistema (`export DATABASE_URL=...`)
2. **Arquivo `.env`** definido em `Config.env_file`
3. **Valores padrão** definidos na classe

## 📝 Notas

- Campos sem valor padrão são **obrigatórios** — a aplicação falha ao iniciar se não estiverem definidos.
- O `settings` é tipicamente exportado como singleton no nível do módulo.
- Para o conceito básico de `BaseSettings`, veja [[BaseSettings + env]].

---
#python #pydantic #env 