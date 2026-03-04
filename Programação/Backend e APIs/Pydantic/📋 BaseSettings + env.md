#pydantic #env

O Pydantic é uma biblioteca Python para validação de dados comummente utilizada em aplicações web. Ele fornece um conjunto de recursos para validar as entradas do usuário, mapear dados JSON em objetos Python e serializar objetos Python em JSON, YAML ou outros formatos. Uma das maneiras mais comuns de usar o Pydantic é definir modelos de dados que herdam de `BaseModel`, que permite que você defina campos obrigatórios, tipos de dados esperados, valores padrão e muitos outros recursos.

O `BaseSettings` é uma classe que também é fornecida pelo Pydantic e é usada para carregar valores de configuração de arquivos `.env`. Ao usar o `BaseSettings`, você pode definir os mesmos campos obrigatórios, tipos de dados e valores padrão que são fornecidos pelo `BaseModel`.

Para usar o `BaseSettings` com o Pydantic, você precisará instalar o pacote `python-dotenv`. Depois de instalado, você pode criar uma classe de configuração que herda do `BaseSettings` e define seus campos de configuração. Aqui está um exemplo básico:

```python
from pydantic import BaseSettings

class Config(BaseSettings):
    database_url: str = "postgres://user:password@localhost/database"
    redis_url: str = "redis://localhost:6379/0"

config = Config()
print(config.database_url)
```

Neste exemplo, criamos uma classe `Config` que herda do `BaseSettings`. Em seguida, definimos dois campos de configuração: `database_url` e `redis_url`. Ambos são strings e têm valores padrão definidos. Finalmente, criamos uma instância da classe `Config` e imprimimos o valor de `database_url`.

Quando você executa este exemplo, o valor de `database_url` será "postgres://user:password@localhost/database", a menos que você defina uma variável de ambiente com o mesmo nome.

Para carregar valores de configuração a partir de um arquivo `.env`, basta criar um arquivo `.env` no mesmo diretório do seu script Python e definir suas variáveis de ambiente. Por exemplo:

```
DATABASE_URL=postgres://user:password@localhost/database
REDIS_URL=redis://localhost:6379/0
```

Agora, quando você executa o exemplo acima, o Pydantic lerá o arquivo `.env` e usará os valores definidos nele para inicializar as variáveis de configuração.
