# 📐 Streamlit — Componentes de Layout

Snippets úteis de componentes de layout do Streamlit para organizar o conteúdo da interface.

## 🔽 Expander

Agrupa conteúdo em uma seção recolhível. Útil para informações complementares que não precisam estar sempre visíveis.

```python
import streamlit as st

with st.expander("How I calculated that?"):
    st.markdown("""
    I used the [Fundamentus](https://www.fundamentus.com.br/) website to get the data.
    """)
```

## 📑 Tabs

Organiza o conteúdo em abas navegáveis. Ideal para exibir categorias ou visualizações diferentes lado a lado.

> 📖 [Documentação oficial — st.tabs](https://docs.streamlit.io/library/api-reference/layout/st.tabs)

```python
import streamlit as st

tab1, tab2, tab3 = st.tabs(["Cat", "Dog", "Owl"])

with tab1:
    st.header("A cat")
    st.image("https://static.streamlit.io/examples/cat.jpg", width=200)

with tab2:
    st.header("A dog")
    st.image("https://static.streamlit.io/examples/dog.jpg", width=200)

with tab3:
    st.header("An owl")
    st.image("https://static.streamlit.io/examples/owl.jpg", width=200)
```

---
#python #streamlit #frontend #layout