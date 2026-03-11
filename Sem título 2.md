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
        env_nested_delimiter='__',  # importante para REDIS__HOST etc.
        # sem env_prefix
    )

    redis: RedisConfig
    weaviate: WeaviateConfig


REDIS__HOST=localhost
REDIS__PORT=6379

WEAVIATE__HOST=localhost
WEAVIATE__PORT=8080
