
```python
with st.expander("How I calculated that?"):  
st.markdown("""  
I used the [Fundamentus](https://www.fundamentus.com.br/) website to get the data.  
""")
```


https://docs.streamlit.io/library/api-reference/layout/st.tabs

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