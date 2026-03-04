# 🗄️ Guia Completo e Configuração: Alembic + SQLAlchemy

Este documento é a base de conhecimento e configuração técnica para o gerenciamento de migrações. Ele utiliza **Pydantic Settings** para ambiente e uma lógica avançada no `env.py` para detecção automática de modelos e suporte a operações seguras.

---

## 🛠️ Instalação das Dependências

Instale os pacotes necessários para suportar a gestão de ambiente e as migrações:

```bash
pip install alembic pydantic_settings sqlalchemy
```

---

## ⚙️ 1. Gestão de Ambiente (`settings.py`)

Abaixo está a implementação da classe `Settings`. Ela garante que as credenciais do banco sejam carregadas corretamente de um arquivo `.env`.

### Explicação:
- **BaseSettings**: Valida automaticamente se as variáveis existem.
- **@property database_url**: Centraliza a construção da string de conexão, facilitando a troca entre SQLite (dev) e PostgreSQL (prod).

### Código Completo:
```python
from pydantic_settings import BaseSettings  
  
class Settings(BaseSettings):  
    POSTGRES_USER: str  
    POSTGRES_PASSWORD: str  
    POSTGRES_DATABASE: str  
    POSTGRES_HOST: str  
  
    @property  
    def database_url(self) -> str:  
        # Alternar conforme a necessidade do ambiente
        return "sqlite:///app.db"  
        # return (f"postgresql+psycopg2://"  
        #         f"{self.POSTGRES_USER}:"        
        #         f"{self.POSTGRES_PASSWORD}@"        
        #         f"{self.POSTGRES_HOST}/"        
        #         f"{self.POSTGRES_DATABASE}")  
        
    class Config:  
        env_file = ".env"  
  
settings = Settings()
```

---

## 🧬 2. Configuração do Alembic (`env.py`)

O arquivo `env.py` abaixo contém lógica para automatizar a descoberta de modelos e tornar as migrações mais resilientes.

### Principais Funcionalidades Implementadas:
1.  **Auto-Discovery**: O bloco `importlib` percorre a pasta de modelos para que o `target_metadata` identifique todas as tabelas automaticamente.
2.  **Include Object**: A função `include_object` evita que o Alembic tente apagar tabelas que existem no banco mas não estão no código (útil para bancos compartilhados).
3.  **Operation Rewriter**: Adiciona `IF NOT EXISTS` e `IF EXISTS` em todas as operações de criação/remoção de tabelas e índices.

### Código Completo do `env.py`:
```python
import importlib  
import pkgutil  
import sys  
from logging.config import fileConfig  
from pathlib import Path  
  
from alembic import context  
from alembic.autogenerate import rewriter  
from alembic.operations import ops  
from sqlalchemy import engine_from_config  
from sqlalchemy import pool  
  
from src.config.settings import settings  
from src.infrastructure.repositories.postgres.connector import Base  
  
# --- Lógica de Auto-Import de Modelos ---
_package_name = "src.infrastructure.repositories.postgres.models"  
try:  
    importlib.import_module(_package_name)  
except ModuleNotFoundError:  
    # Resolve o path do projeto caso o script seja executado de subpastas
    project_root = Path(__file__).resolve().parents[1]  
    sys.path.insert(0, str(project_root))  
    importlib.invalidate_caches()  
  
try:  
    _pkg = importlib.import_module(_package_name)  
    if hasattr(_pkg, "__path__"):  
        for _finder, _name, _ispkg in pkgutil.iter_modules(_pkg.__path__):  
            if _name.startswith("_"):  
                continue  
            importlib.import_module(f"{_package_name}.{_name}")  
except Exception:  
    raise RuntimeError(f"O pacote de modelos não pôde ser importado: {_package_name}.")  

# --- Configurações Padrão do Alembic ---
config = context.config  
if config.config_file_name is not None:  
    fileConfig(config.config_file_name)  
  
target_metadata = Base.metadata  
  
def include_object(obj, name, type_, reflected, compare_to):  
    # Pula tabelas que existem apenas no banco (evita drop_table indesejado)
    if type_ == "table" and reflected and compare_to is None:  
        return False  
    return True  
  
# --- Rewriter para Operações Seguras (IF NOT EXISTS) ---
writer = rewriter.Rewriter()  
  
@writer.rewrites(ops.CreateTableOp)  
@writer.rewrites(ops.CreateIndexOp)  
def add_if_not_exists(context, revision, op):  
    op.if_not_exists = True  
    return op  
  
@writer.rewrites(ops.DropTableOp)  
@writer.rewrites(ops.DropIndexOp)  
def add_if_exists(context, revision, op):  
    op.if_exists = True  
    return op  
  
# --- Execução das Migrações ---
def run_migrations_offline() -> None:  
    context.configure(  
        url=settings.database_url,  
        target_metadata=target_metadata,  
        literal_binds=True,  
        dialect_opts={"paramstyle": "named"},  
        include_object=include_object,  
        process_revision_directives=writer  
    )  
  
    with context.begin_transaction():  
        context.run_migrations()  
  
def run_migrations_online() -> None:  
    connectable = engine_from_config(  
        config.get_section(config.config_ini_section, {}),  
        prefix="sqlalchemy.",  
        poolclass=pool.NullPool,  
        url=settings.database_url  
    )  
  
    with connectable.connect() as connection:  
        context.configure(  
            connection=connection,  
            target_metadata=target_metadata,  
            include_object=include_object,  
            process_revision_directives=writer  
        )  
  
        with context.begin_transaction():  
            context.run_migrations()  
  
if context.is_offline_mode():  
    run_migrations_offline()
else:  
    run_migrations_online()
```

---

## 🚀 Fluxo de Trabalho (Workflow)

1.  **Criar Revisão**: `alembic revision --autogenerate -m "sua_mensagem"`
2.  **Aplicar**: `alembic upgrade head`
3.  **Voltar**: `alembic downgrade -1`

---
#python #sqlalchemy #alembic #migrations #database
