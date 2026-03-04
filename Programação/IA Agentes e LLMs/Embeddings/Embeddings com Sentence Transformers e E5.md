# Documentação: Gerando Embeddings com Sentence Transformers (E5)

Esta documentação explica como converter textos em vetores numéricos (**embeddings**) utilizando a biblioteca `sentence-transformers` e o modelo bilíngue/multilíngue **E5**.

---

## 1. O que são Embeddings?
Embeddings são representações matemáticas de palavras ou frases em um espaço vetorial de múltiplas dimensões. Textos com significados semânticos próximos ficam posicionados "perto" um do outro nesse espaço, permitindo buscas semânticas, agrupamentos e classificação de textos.

## 2. Requisitos e Instalação
Para rodar o código, você precisará das bibliotecas `torch` (PyTorch) e `sentence-transformers`.

```bash
pip install torch sentence-transformers
```

---

## 3. Código Exemplo

```python
import numpy as np
import torch
from sentence_transformers import SentenceTransformer

# 1. Escolha do modelo (Multilingual E5 Small é eficiente e poderoso)
model_name = "intfloat/multilingual-e5-small"

# 2. Configuração de Hardware (Usa GPU se disponível, caso contrário CPU)
device = "cuda" if torch.cuda.is_available() else "cpu"

# 3. Carregamento do modelo
model = SentenceTransformer(model_name, device=device)

# 4. Texto de entrada
text = "Olá, como vai você?"

# 5. Geração do Embedding
# convert_to_numpy=True garante que o retorno seja um array NumPy
embedding: np.ndarray = model.encode(text, convert_to_numpy=True)

# Exibição dos primeiros 10 valores do vetor
print(f"Dimensões do vetor: {len(embedding)}")
print(f"Embedding (primeiros 10): {embedding[:10]}...")
```

---

## 4. Explicação dos Componentes

### A. O Modelo `multilingual-e5-small`
O modelo **E5 (EmbEddings from bidirectionAl Encoder representations)** da Microsoft é um dos mais populares para recuperação de informação. A versão `small` é leve, rápida e suporta diversos idiomas, incluindo o Português.
- **Nota Técnica:** Para modelos E5, recomenda-se adicionar o prefixo `query: ` ou `passage: ` antes do texto para melhorar a precisão em tarefas de busca (ex: `query: Como extrair NF-e`).

### B. Gerenciamento de Device (`torch`)
O código detecta automaticamente se há uma GPU NVIDIA disponível (`cuda`). Usar GPU acelera drasticamente a geração de embeddings quando processamos grandes volumes de texto em lote (batch).

### C. `model.encode()`
Este é o método principal que:
1. Tokeniza o texto (transforma em números que o modelo entende).
2. Passa os tokens pela rede neural (Transformer).
3. Realiza o **Pooling** (normalmente extrai a média dos vetores) para gerar um único vetor final que representa a frase toda.

---

## 5. Aplicações Comuns
- **Busca Semântica:** Comparar o vetor de uma pergunta com vetores de documentos em um banco de dados (ex: Weaviate, Pinecone, ChromaDB).
- **RAG (Retrieval Augmented Generation):** Recuperar contexto relevante para enviar a um LLM.
- **Detecção de Duplicados:** Encontrar frases que dizem a mesma coisa de formas diferentes.

---

## 6. ModelLoaderService — Classe Reutilizável

Encapsula o carregamento do modelo em uma classe com detecção automática de device (GPU/CPU). Carrega o modelo uma única vez no `__init__` e expõe via `property`.

```python
import torch
from sentence_transformers import SentenceTransformer


class ModelLoaderService:
    def __init__(self, model_name: str):
        self.model_name = model_name
        self.device = "cuda" if torch.cuda.is_available() else "cpu"
        self.model_instance = None
        self.load_model()

    def load_model(self):
        if self.model_instance is None:
            try:
                self.model_instance = SentenceTransformer(self.model_name, device=self.device)
            except Exception as e:
                raise RuntimeError(f"Failed to load model '{self.model_name}': {e}")

    @property
    def model(self) -> SentenceTransformer:
        return self.model_instance
```

### Uso

```python
model_loader = ModelLoaderService("intfloat/multilingual-e5-small")

# Gerar embedding
embedding = model_loader.model.encode("Olá, como vai?", convert_to_numpy=True)
```

> Ideal para injetar como dependência em serviços que precisam do modelo (ex: FastAPI, pipelines de RAG).

---

## 7. Dicas de Performance
Se você tiver uma lista de textos, passe a lista inteira para o `model.encode(lista_de_textos)` em vez de fazer um loop. O modelo processará em **batch**, aproveitando muito melhor o hardware.

---
#nlp #embeddings #python #ia #machine-learning #huggingface #sentence-transformers #e5
