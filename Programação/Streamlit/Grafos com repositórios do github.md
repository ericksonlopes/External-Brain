#github #beautifulsoup #streamlit #requests #matplotlib #networkx

```python
# pip install matplotlib
# pip install beautifulsoup4
# pip install networkx
# pip install requests
# pip install streamlit

import matplotlib.pyplot as plt
import networkx as nx
import requests
import streamlit as st
from bs4 import BeautifulSoup


def build_graph(url, graph, progress_bar):
    req = requests.get(url)
    soup = BeautifulSoup(req.text, "html.parser")
    list_items = soup.find(class_="js-details-container Details")

    num_rows = len(list_items.find_all(role='row')[1:])

    for i, row in enumerate(list_items.find_all(role='row')[1:]):

        name = row.text.split()[0].replace('\n', '')

        if name == ".":
            continue

        try:
            type_ = row.svg['aria-label']
            link = 'https://github.com' + str(row.span.a['href'])
            node = link.split("/")[-1]

            if name != "Go to parent directory":
                if 'Directory' == type_:
                    graph.add_node(node)
                    graph.add_edge(link.split("/")[-2], node)

                    text = f"Processando {name} ({i + 1}/{num_rows})"
                    # Atualiza a barra de progresso a cada iteração do loop
                    progress_bar.progress((i + 1) / num_rows, text=text)

                    graph = build_graph(link, graph, progress_bar)

                else:
                    graph.add_node(node)
                    graph.add_edge(link.split("/")[-2], node)

        except Exception as e:
            print(e, name)

    progress_bar.progress(1.0, text="Concluído!")
    return graph


@st.cache_data
def load_data():
    return "https://github.com/ericksonlopes/SearchInExportChat-StreamLit"


repo_url = st.text_input("Digite o repositório: ", value=load_data())

nx_graph = nx.Graph()

with st.spinner('Carregando...'):
    # Cria uma barra de progresso
    st_progress_bar = st.progress(0, text=repo_url)

    nx_graph = build_graph(repo_url, nx_graph, st_progress_bar)

# Define a posição dos nós com espaçamento
pos = nx.spring_layout(nx_graph, seed=60, k=1.5, iterations=200)

# Define o tamanho da figura
fig, ax = plt.subplots(figsize=(10, 10))

# Desenha o grafo
nx.draw(nx_graph, pos, with_labels=True, node_size=600, ax=ax)

# Exibe a imagem com o Streamlit
st.pyplot(fig)

```