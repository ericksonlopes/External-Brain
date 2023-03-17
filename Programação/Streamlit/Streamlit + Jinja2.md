#streamlit #jinja2

O Jinja2 é uma engine de template muito popular em Python que permite criar templates HTML, XML e outros tipos de arquivos de forma mais fácil e intuitiva. Para utilizá-lo no Streamlit, é necessário instalar o pacote jinja2 e importá-lo em seu código.

Aqui está um exemplo básico de como usar o Jinja2 no Streamlit:

```python
import streamlit as st
from jinja2 import Template

template = Template("""
{% for item in items %}
<p>{{ item }}</p>
{% endfor %}
""")

items = ["item 1", "item 2", "item 3"]
html = template.render(items=items)

st.markdown(html, unsafe_allow_html=True)
```

Nesse exemplo, a variável `template` contém o template HTML que será renderizado pelo Jinja2. Em seguida, a variável `items` é uma lista de itens que será passada para o template e renderizada na página. Por fim, a variável `html` contém o resultado final do processamento do template pelo Jinja2, que é exibido na página do Streamlit usando a função `st.markdown()`.

O resultado desse código seria a exibição de uma lista com os itens "item 1", "item 2" e "item 3". Obviamente, o Jinja2 é muito mais poderoso do que isso e permite a criação de templates complexos com lógica condicional, loops, etc. Para saber mais sobre o Jinja2 e suas funcionalidades, consulte a documentação oficial: [https://jinja.palletsprojects.com/en/3.0.x/](https://jinja.palletsprojects.com/en/3.0.x/)