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
    {"id": "14", "content": "exemplo de outro texto para exemplo"},

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

# Imprimir os grupos de textos similares com porcentagem de similaridade
for idx, group in enumerate(groups, 1):
    text_group = [lista_de_objetos[i]['content'] for i in group]
    avg_similarity = sum(similarities[i, j] for i, j in combinations(group, 2)) / len(group)
    print(f"Grupo {idx}: {text_group}")
    print(f"  - Porcentagem de Similaridade: {avg_similarity:.2%}")
    print("-----")

```

Este código em Python realiza a análise de similaridade entre textos usando a técnica TF-IDF (Term Frequency-Inverse Document Frequency) e o cosseno como medida de similaridade. Vou explicar cada parte do código em detalhes.

### Importação de Bibliotecas
```python
from itertools import combinations
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
```
- **itertools**: Importa a função `combinations` que será utilizada para gerar combinações de índices para comparar pares de textos.
- **TfidfVectorizer**: Importa a classe que será utilizada para converter os textos em uma matriz TF-IDF.
- **cosine_similarity**: Importa a função que será utilizada para calcular a similaridade do cosseno entre os textos.

### Lista de Objetos
```python
lista_de_objetos = [
    {"id": "1", "content": "exemplo de texto"},
    # ... (outros objetos com id e conteúdo)
]
```
- Define uma lista de objetos, onde cada objeto possui uma identificação (`id`) e um conteúdo de texto (`content`).

### Extração de Textos
```python
texts = [obj["content"] for obj in lista_de_objetos]
```
- Cria uma lista `texts` contendo apenas os conteúdos de texto dos objetos da lista.

### Criação da Matriz TF-IDF
```python
vectorizer = TfidfVectorizer()
tfidf_matrix = vectorizer.fit_transform(texts)
```
- Inicializa um `TfidfVectorizer` que converte os textos em uma matriz TF-IDF.
- A matriz TF-IDF é criada a partir dos textos fornecidos.

### Cálculo de Similaridades
```python
similarities = cosine_similarity(tfidf_matrix)
```
- Calcula as similaridades entre os textos utilizando a medida de similaridade do cosseno.

### Agrupamento de Textos Similares
```python
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
```
- Define um limiar (`threshold`) para determinar se dois textos são considerados similares.
- Utiliza a combinação de índices para comparar todos os pares de textos.
- Cria grupos de textos similares com base na similaridade.

### Impressão dos Resultados
```python
for idx, group in enumerate(groups, 1):
    text_group = [lista_de_objetos[i]['content'] for i in group]
    avg_similarity = sum(similarities[i, j] for i, j in combinations(group, 2)) / len(group)
    print(f"Grupo {idx}: {text_group}")
    print(f"  - Porcentagem de Similaridade: {avg_similarity:.2%}")
    print("-----")
```
- Itera sobre os grupos de textos similares e imprime as informações.
- Exibe os textos pertencentes a cada grupo e a porcentagem média de similaridade dentro do grupo.

### Observação:
- Existe um pequeno erro na lista de objetos onde dois objetos têm a mesma chave "id" com o valor "14". Para corrigir, você pode ajustar os valores das chaves "id" para torná-las únicas.

### Resultado:
```
Grupo 1: ['exemplo de texto', 'outro exemplo de texto', 'outro exemplo de coluna', 'exemplo de outro texto para exemplo']
  - Porcentagem de Similaridade: 103.29%
-----
Grupo 2: ['bananas e pijamas', 'pijamas e bananas']
  - Porcentagem de Similaridade: 50.00%
-----
Grupo 3: ['caramelo cachorro', 'cachorro caramelo']
  - Porcentagem de Similaridade: 50.00%
-----
Grupo 4: ['botas de couro a venda', 'venda seu imovel mais barato com botas de couro']
  - Porcentagem de Similaridade: 28.35%
-----
```