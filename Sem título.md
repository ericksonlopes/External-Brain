```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

from bee_news.repository import NewsRepository


def find_text_similarity(text, list_text):
    # Extrair os textos da lista
    texts = [obj["content"] for obj in list_text]

    texts.append(text)

    vectorized = TfidfVectorizer()
    tfidf_matrix = vectorized.fit_transform(texts)

    similarities = cosine_similarity(tfidf_matrix)

    texto_index = len(list_text)

    similar_texts = []
    for i, _ in enumerate(list_text):
        if similarities[texto_index, i] > threshold:
            similar_texts.append(list_text[i]['content'])

    return similar_texts


news_repository = NewsRepository()
list_news = news_repository.get_last_24h_by_category("Política")

texto_parametro = "O ministro do Supremo Tribunal Federal (STF), Cristiano Zanin, atendeu a um pedido do governo Lula e suspendeu trechos da lei que prorrogava a desoneração da folha de pagamento para dezessete setores da economia. A decisão foi tomada através de uma liminar, mas ainda precisa ser referendada pelo plenário do STF. Caso seja confirmada, a suspensão ficará em vigor até o próprio Supremo analisar o mérito da questão."
threshold = 0.27  # Ajuste este valor conforme necessário

textos_similares = find_text_similarity(texto_parametro, list_news)
print(f"Textos similares ao texto '{texto_parametro}':")
for texto in textos_similares:
    print()
    print(f"- {texto}")


```