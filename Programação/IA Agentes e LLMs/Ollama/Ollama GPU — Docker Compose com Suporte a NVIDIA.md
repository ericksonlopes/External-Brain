# 🐳 Ollama GPU — Docker Compose com Suporte a NVIDIA

Configuração do Docker Compose para rodar o **Ollama** com aceleração de hardware (GPU NVIDIA) e exemplo de integração com LangChain.

## 📦 Dependências

- Docker & Docker Compose
- NVIDIA Container Toolkit instalado no host

## ⚙️ Implementação

### Docker Compose
`yaml
version: '3.8'
services:
  ollama:
    image: ollama/ollama:latest
    container_name: ollama-gpu
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]
    environment:
      - NVIDIA_VISIBLE_DEVICES=all
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:11434/api/tags"]
      interval: 30s
      timeout: 10s
      retries: 3

volumes:
  ollama_data:
`

## 🧪 Exemplo de Uso (LangChain)

`python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_ollama import ChatOllama

# Setup do LLM apontando para o container
llm = ChatOllama(model="llama3.2", base_url="http://localhost:11434")
prompt = ChatPromptTemplate.from_template("Pergunta: {input}\nResponda em PT:")
chain = prompt | llm | StrOutputParser()

# Invocação
response = chain.invoke({"input": "Como funciona o Docker?"})
print(response)
`

## 📝 Tabela de Configuração

| Variável | Descrição |
|----------|-----------|
| 11434 | Porta padrão da API do Ollama. |
| ollama_data | Volume persistente para armazenar modelos baixados. |

## 📌 Notas

- Certifique-se de baixar o modelo manualmente ou via script: docker exec -it ollama-gpu ollama pull llama3.2.
- O healthcheck garante que o serviço só seja considerado "saudável" quando a API responder.

> 🔗 Veja também: [[ContextualCompressionRetriever — Recuperação com Re-rank no LangChain]]

---
#docker #ia #llm #ollama #gpu
