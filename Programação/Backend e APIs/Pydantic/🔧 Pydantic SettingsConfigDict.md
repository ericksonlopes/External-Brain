# 🔧 Configurações — Pydantic Settings para Redis e Weaviate

Este documento descreve a classe de configurações baseada em `pydantic-settings` usada para carregar parâmetros tipados de Redis e Weaviate a partir de um arquivo `.env` ou das variáveis de ambiente do sistema. Utiliza `env_nested_delimiter='__'` para mapear variáveis aninhadas (ex.: `REDIS__HOST` → `settings.redis.host`). Recomendado para centralizar e validar configurações em projetos Python.

```
.env ──▶ Settings(BaseSettings)
            ├─ redis: RedisConfig
            └─ weaviate: WeaviateConfig
```

## 📦 Dependências

```bash
pip install "pydantic>=2.0" pydantic-settings
```

## ⚙️ Implementação

```python
from pydantic import BaseModel
from pydantic_settings import BaseSettings, SettingsConfigDict


class RedisConfig(BaseModel):
    host: str
    port: int


class WeaviateConfig(BaseModel):
    host: str
    port: int


class Settings(BaseSettings):
    model_config = SettingsConfigDict(
        env_file='.env',
        env_nested_delimiter='__',  # enables nested env variables like REDIS__HOST
        # no env_prefix
    )

    redis: RedisConfig
    weaviate: WeaviateConfig
```

## 🧪 Exemplo de Uso

Arquivo `.env` de exemplo (no mesmo diretório do projeto):

```
REDIS__HOST=localhost
REDIS__PORT=6379

WEAVIATE__HOST=localhost
WEAVIATE__PORT=8080
```

Exemplo mínimo de uso em Python:

```python
# suposição: o código acima está em config.py
from config import Settings

# Ao instanciar, pydantic-settings carrega .env e variáveis de ambiente
settings = Settings()

# Acesso tipado aos valores
print(settings.redis.host)      # 'localhost'
print(settings.redis.port)      # 6379
print(settings.weaviate.host)   # 'localhost'
print(settings.weaviate.port)   # 8080
```

Para sobrescrever em runtime, exporte as variáveis de ambiente antes de executar:

Linux/macOS:
```bash
export REDIS__HOST=redis.prod
export WEAVIATE__HOST=weaviate.prod
python main.py
```

Windows (PowerShell):
```powershell
$env:REDIS__HOST = "redis.prod"
$env:WEAVIATE__HOST = "weaviate.prod"
python main.py
```

## 📝 Tabela de Conceitos / Configuração

| Conceito | Descrição |
|----------|-----------|
| `Settings` | Classe derivada de `BaseSettings` que centraliza e valida configurações tipadas. |
| `env_file` | Arquivo onde variáveis de ambiente podem ser definidas (ex.: `.env`). |
| `env_nested_delimiter` | Delimitador usado para mapear variáveis aninhadas: `REDIS__HOST` → `settings.redis.host`. |
| `RedisConfig` / `WeaviateConfig` | Modelos `BaseModel` que tipam os campos esperados para cada serviço. |
| `REDIS__HOST`, `REDIS__PORT` | Exemplos de variáveis de ambiente que populam `settings.redis`. |

## 🔗 Notas

- Não commitar arquivos `.env` com credenciais sensíveis; usar secret manager em produção.
- Se algum campo for opcional, declare-o com `Optional[...]` e forneça um `default` no `BaseModel`.
- `pydantic` validará tipos na inicialização e levantará `ValidationError` se valores estiverem ausentes ou inválidos.
- O `env_nested_delimiter` é útil para agrupar configurações por serviço sem precisar de prefixes distintos.

---
#python #pydantic #env
