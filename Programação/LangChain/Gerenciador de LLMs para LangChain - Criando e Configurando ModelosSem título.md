

## Uma introdução ao uso de LLMs com LangChain utilizando Pydantic para configuração.  
  
# Introdução  
  
No mundo da inteligência artificial, os Modelos de Linguagem de Grande Escala (LLMs) têm se destacado na geração de texto, tradução e em muitas outras tarefas. O LangChain é uma biblioteca que facilita a integração de LLMs em aplicações. Neste artigo, iremos explorar como criar um gerenciador de LLMs utilizando a biblioteca LangChain e a biblioteca Pydantic para a configuração.  
  
## Conceitos Principais  
  
Antes de começarmos a implementação, vamos revisar alguns conceitos que serão utilizados:  
  
### Enum  
  
O `Enum` é uma classe que permite criar um conjunto de constantes nomeadas. No nosso caso, utilizaremos para definir as plataformas que suportam os LLMs:  
  
```python  
from enum import Enum  
  
class LLMPlatform(str, Enum):  
    OPENAI = "openai"
```  
  
### Pydantic  
  
O Pydantic é uma biblioteca que facilita a validação de dados através de classes baseadas em tipos. Utilizaremos o Pydantic para definir a configuração dos nossos modelos:  
  
```python  
from pydantic import BaseModel, Field, SecretStr  
```  
  
## Definindo a Configuração do Modelo  
  
Agora que temos uma ideia básica dos conceitos, vamos definir a configuração para nossos LLMs.  
  
### Classe LLMConfig  
  
Essa classe servirá como base para a configuração dos modelos:  
  
```python  
from pydantic import BaseModel, Field, SecretStr

class LLMConfig(BaseModel):  
    model: str = Field(description="Nome do modelo a ser utilizado.")  
    platform: LLMPlatform | None = Field(default=None, description="Plataforma onde o modelo está hospedado.")  
    max_retries: int = Field(default=2, description="Número máximo de tentativas em caso de falha.")  
    temperature: float = Field(default=0.7, description="Temperatura de amostragem, controlando a criatividade.")  
```  
  
### Classe LLMConfigOpenai  
  
Aqui, estendemos a `LLMConfig` para incluir a chave da API da OpenAI:  
  
```python  
class LLMConfigOpenai(LLMConfig):  
    api_key: SecretStr = Field(description="Chave da API para serviços OpenAI.")  
```  
  
### Configuração de Modelos  
  
Agora, podemos definir uma configuração padrão para nossos modelos:  
  
```python  
models_config: Dict[str, LLMConfig] = {    "gpt-4o-mini": LLMConfigOpenai(  
        model="gpt-4o-mini",  
        platform=LLMPlatform.OPENAI,  
        api_key=SecretStr("your-openai-api-key-here"),  
    )}  
```  
  
## Fábrica de LLMs  
  
Para facilitar a criação dos modelos, vamos criar uma classe `LLMFactory` que será responsável por instanciar e retornar os LLMs:  
  
```python  
from langchain_core.runnables import Runnable
from langchain_openai import ChatOpenAI

class LLMFactory(LLMConfig):
    def __init__(self, /, **data: Any):
        super().__init__(**data)
        self._models_config: Dict[str, LLMConfig] = data.get("models_config", models_config)

    def get_llm(self) -> Runnable:
        found: LLMConfig = self._models_config.get(self.model)

        if not found:
            raise ValueError(f"Modelo '{self.model}' não encontrado.")

        model_conf = self._models_config[self.model]
        provider = getattr(model_conf, "provider", "").lower()

        try:
            caller_values = self.model_dump(exclude_unset=True)

            model_conf_values = model_conf.model_dump(exclude_unset=True)
            for key in model_conf_values.keys():
                caller_values.pop(key, None)

            merged_conf = model_conf.model_copy(update=caller_values)

            if provider == "openai":
                llm = self._create_openai_llm_azure(merged_conf)

            else:
                raise ValueError(f"Provedor '{provider}' não suportado")

            return llm

        except Exception as error:
            raise RuntimeError(
                f"Erro ao criar LLM (model={self.model}, provider={provider}): {str(error)}"
            ) from error

    @classmethod
    def _create_openai_llm(cls, config: LLMConfigOpenai) -> Runnable:
        return ChatOpenAI(
            api_key=config.api_key,
            temperature=config.temperature,
            max_retries=config.max_retries,
        )
```  
  
### Explicação da Fábrica  
  
- **Construtor**: O construtor inicializa a configuração do modelo e armazena a configuração dos modelos disponíveis.  
- **Método `get_llm`**: Esse método é responsável por retornar a instância do LLM. Ele verifica se o modelo existe e cria a instância correspondente, tratando erros ao longo do caminho.  
- **Método `_create_openai_llm`**: Este método específico cria uma instância do modelo da OpenAI utilizando a classe `ChatOpenAI`.  
  
## Boas Práticas e Dicas  
  
1. **Validação de Dados**: Sempre utilize o Pydantic para validar a entrada de dados. Isso garante que você está usando as configurações corretas.  
2. **Gerenciamento de Erros**: Implemente tratamento de erros adequado para melhorar a robustez da aplicação.  
3. **Segurança**: Nunca exponha suas chaves de API. Utilize variáveis de ambiente ou serviços de gerenciamento de segredos.  
  
## Conclusão  
  
Neste artigo, exploramos como criar um gerenciador de LLMs utilizando LangChain e Pydantic. Com a configuração adequada, você pode facilmente integrar diferentes modelos de linguagem em suas aplicações. Sinta-se à vontade para adaptar o código e expandir suas funcionalidades conforme necessário!  
  
  
## Exemplos de Código  
  
### Exemplo 1  
```python  
from enum import Enum  
  
class LLMPlatform(str, Enum):  
    OPENAI = "openai"```  
  
### Exemplo 2  
```python  
from pydantic import BaseModel, Field, SecretStr  
  
class LLMConfig(BaseModel):  
    model: str = Field(description="Nome do modelo a ser utilizado.")  
    platform: LLMPlatform | None = Field(default=None, description="Plataforma onde o modelo está hospedado.")  
    max_retries: int = Field(default=2, description="Número máximo de tentativas em caso de falha.")  
    temperature: float = Field(default=0.7, description="Temperatura de amostragem, controlando a criatividade.")  
```  
  
### Exemplo 3  
```python  
models_config: Dict[str, LLMConfig] = {    "gpt-4o-mini": LLMConfigOpenai(  
        model="gpt-4o-mini",  
        platform=LLMPlatform.OPENAI,  
        api_key=SecretStr("your-openai-api-key-here"),  
    )}  
```  
  
### Exemplo 4  
```python  
class LLMFactory(LLMConfig):  
    def __init__(self, /, **data: Any):  
        super().__init__(**data)  
        self._models_config: Dict[str, LLMConfig] = data.get("models_config", models_config)  
  
    def get_llm(self) -> Runnable:  
        found: LLMConfig = self._models_config.get(self.model)  
  
        if not found:            raise ValueError(f"Modelo '{self.model}' não encontrado.")  
  
        model_conf = self._models_config[self.model]  
        provider = getattr(model_conf, "provider", "").lower()  
  
        try:            caller_values = self.model_dump(exclude_unset=True)  
  
            model_conf_values = model_conf.model_dump(exclude_unset=True)  
            for key in model_conf_values.keys():  
                caller_values.pop(key, None)  
  
            merged_conf = model_conf.model_copy(update=caller_values)  
  
            if provider == "openai":                llm = self._create_openai_llm(merged_conf)  
  
            else:                raise ValueError(f"Provedor '{provider}' não suportado")  
  
            return llm  
        except Exception as error:            raise RuntimeError(  
                f"Erro ao criar LLM (model={self.model}, provider={provider}): {str(error)}"  
            ) from error  
    @classmethod  
    def _create_openai_llm(cls, config: LLMConfigOpenai) -> Runnable:  
        return ChatOpenAI(  
            api_key=config.api_key,  
            temperature=config.temperature,  
            max_retries=config.max_retries,  
        )```  
  
  
---  
*Este artigo foi elaborado para ajudar desenvolvedores a entender como integrar LLMs em suas aplicações usando LangChain.*  
  
---  
#LangChain #LLM #Pydantic #Python #OpenAI 