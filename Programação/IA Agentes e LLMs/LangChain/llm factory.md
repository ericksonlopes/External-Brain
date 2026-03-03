#pydantic #langchain #openai #langchain_core

```python
from enum import Enum  
from typing import Any, Dict  
  
from langchain_core.runnables import Runnable  
from langchain_openai import ChatOpenAI  
from pydantic import BaseModel, Field, SecretStr  
  
  
class LLMPlatform(str, Enum):  
    OPENAI = "openai"  
  
  
class LLMConfig(BaseModel):  
    """Base settings for a Large Language Model."""  
    model: str = Field(description="Name of the model to be used.")  
    platform: LLMPlatform | None = Field(default=None, description="Platform where the model is hosted.")  
    max_retries: int = Field(default=2, description="Maximum number of retries in case of failure.")  
    temperature: float = Field(default=0.7, description="Sampling temperature, controlling creativity.")  
  
  
class LLMConfigOpenai(LLMConfig):  
    api_key: SecretStr = Field(description="API key for OpenAI services.")  
  
  
models_config: Dict[str, LLMConfig] = {  
    "gpt-4o-mini": LLMConfigOpenai(  
        model="gpt-4o-mini",  
        platform=LLMPlatform.OPENAI,  
        api_key=SecretStr("your-openai-api-key-here"),  
    )}  
  
  
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