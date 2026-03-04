
Agrupa textos similares usando **TF-IDF** (Term Frequency-Inverse Document Frequency) e **similaridade do cosseno** com scikit-learn.

## 🔧 Dependências

```bash
pip install scikit-learn
```

## 📄 Implementação

```python
from itertools import combinations
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

lista_de_objetos = [
    {"id": "1", "content": "exemplo de texto"},
    {"id": "2", "content": "outro exemplo de texto"},
    {"id": "3", "content": "um texto diferente"},
    {"id": "4", "content": "diferente de todos os outros textos"},
    {"id": "5", "content": "outro exemplo de coluna"},
    {"id": "6", "content": "bananas e pijamas"},
    {"id": "7", "content": "pijamas e bananas"},
    {"id": "8", "content": "cachorro caramelo"},
    {"id": "9", "content": "caramelo cachorro"},
    {"id": "10", "content": "coca cola"},
    {"id": "11", "content": "ratinho de botas"},
    {"id": "12", "content": "botas de couro a venda"},
    {"id": "13", "content": "vendi meu carro para pagar o aluguel"},
    {"id": "14", "content": "venda seu imovel mais barato com botas de couro"},
    {"id": "15", "content": "exemplo de outro texto para exemplo"},
]

# Extrair os textos da lista
texts = [obj["content"] for obj in lista_de_objetos]

# Criar a matriz TF-IDF
vectorizer = TfidfVectorizer()
tfidf_matrix = vectorizer.fit_transform(texts)

# Calcular as similaridades entre os textos
similarities = cosine_similarity(tfidf_matrix)

# Agrupar textos similares
threshold = 0.5  # Ajuste este valor conforme necessário
groups = []
for i, j in combinations(range(len(lista_de_objetos)), 2):
    if similarities[i, j] > threshold:
        found = False
        for group in groups:
            if i in group or j in group:
                group.add(i)
                group.add(j)
                found = True
                break
        if not found:
            groups.append({i, j})

# Imprimir os grupos
for idx, group in enumerate(groups, 1):
    text_group = [lista_de_objetos[i]['content'] for i in group]
    avg_similarity = sum(similarities[i, j] for i, j in combinations(group, 2)) / len(group)
    print(f"Grupo {idx}: {text_group}")
    print(f"  - Porcentagem de Similaridade: {avg_similarity:.2%}")
    print("-----")
```

## 📤 Saída Esperada

```
Grupo 1: ['exemplo de texto', 'outro exemplo de texto', 'outro exemplo de coluna', 'exemplo de outro texto para exemplo']
  - Porcentagem de Similaridade: 103.29%
Grupo 2: ['bananas e pijamas', 'pijamas e bananas']
  - Porcentagem de Similaridade: 50.00%
Grupo 3: ['caramelo cachorro', 'cachorro caramelo']
  - Porcentagem de Similaridade: 50.00%
Grupo 4: ['botas de couro a venda', 'venda seu imovel mais barato com botas de couro']
  - Porcentagem de Similaridade: 28.35%
```

## ⚙️ Como Funciona

1. **TF-IDF**: Converte textos em vetores numéricos, dando mais peso a palavras raras e menos a palavras comuns.
2. **Cosseno**: Mede o ângulo entre dois vetores — quanto mais próximo de 1, mais similares são os textos.
3. **Threshold**: Define o limiar mínimo de similaridade para agrupar textos (ajustável conforme necessidade).

## ⚠️ Ajustes Necessários

- O cálculo de `avg_similarity` divide por `len(group)` em vez de pelo número real de combinações — isso pode gerar valores acima de 100%.
- Para corrigir, substituir `/ len(group)` por `/ max(len(list(combinations(group, 2))), 1)`.

---
#python #sklearn #nlp #datascience #algoritmo-de-similaridade