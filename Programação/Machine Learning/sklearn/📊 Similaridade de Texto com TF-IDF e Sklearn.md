Este script utiliza a técnica **TF-IDF (Term Frequency-Inverse Document Frequency)** para transformar textos em vetores numéricos e calcular a similaridade entre eles utilizando a **Similaridade de Cosseno**.

## 🛠️ Pré-requisitos

```bash
pip install scikit-learn
```

## 📄 Código Principal

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

def find_text_similarity(text_parametro, list_news, threshold=0.27):
    """
    Compara um texto parametro contra uma lista de objetos de notícias.
    Retorna o conteúdo das notícias que superam o limite de similaridade.
    """
    # Extrair os textos da lista de objetos
    texts = [obj["content"] for obj in list_news]
    texts.append(text_parametro)

    # Vetorização e cálculo de similaridade
    vectorized = TfidfVectorizer()
    tfidf_matrix = vectorized.fit_transform(texts)
    similarities = cosine_similarity(tfidf_matrix)

    # O texto comparado é o último da matriz
    texto_index = len(list_news)
    similar_texts = []

    for i, _ in enumerate(list_news):
        if similarities[texto_index, i] > threshold:
            similar_texts.append(list_news[i]['content'])

    return similar_texts

# Exemplo de uso
# textos_similares = find_text_similarity(texto_parametro, list_news)
```

## ⚙️ Parâmetros Importantes

- **`threshold`**: Define quão parecidos os textos devem ser (0.0 a 1.0).
    - Valores acima de **0.25** geralmente indicam temas muito próximos.
    - Valores acima de **0.50** indicam textos quase idênticos ou parafraseados.

## 📝 Notas de Uso

Este algoritmo é ideal para:
- Agrupar notícias de um mesmo evento.
- Remover duplicatas em scrapers de notícias.
- Sugerir conteúdos relacionados em blogs ou portais.

---
#python #machine-learning #sklearn #nlp #tfidf
