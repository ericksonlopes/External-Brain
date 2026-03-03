
Este código executa uma **extração iterativa de elementos de um dicionário cujos valores são listas**, até que todas essas listas estejam vazias. Vamos analisar passo a passo:

#dictionary #while #loop #pop #python

#### 🧠 **O que o código faz?**

```python
x = {"economia": ["12", "12"], "investimento": ["21", "22"]}

while True:
    no_news_found = True
    
    for key, value in x.items():
        if value:
            print(value.pop(0))
            no_news_found = False
    
    if no_news_found:
        break

print(x)
```

#### ⚙️ **Como funciona?**

1. **Dicionário `x`** contém duas chaves (`"economia"` e `"investimento"`), cada uma com uma lista de strings.

2. **Loop infinito (`while True`)**: usado para continuar removendo elementos enquanto houver itens nas listas.

3. Dentro do loop:

   * A flag `no_news_found` é definida como `True`.
   * Para cada chave do dicionário, o código verifica se a lista associada ainda possui elementos.
   * Se sim:

     * O primeiro elemento é **removido da lista** com `pop(0)` e impresso.
     * A flag `no_news_found` vira `False`.

4. Se, após a iteração completa, **nenhuma lista tiver elementos**, `no_news_found` permanece `True` e o loop é encerrado com `break`.

5. Por fim, o dicionário `x` é impresso — agora com todas as listas **vazias**.

---

#### 📤 **Saída esperada**:

```
12
21
12
22
{'economia': [], 'investimento': []}
```

#### ✅ **Resumo**:

Este código funciona como um **"esgotador" de filas por chave**, onde os elementos das listas são processados um a um de forma alternada entre as chaves, até que todos os valores sejam consumidos.

Quer que eu transforme esse padrão em uma função reutilizável?
