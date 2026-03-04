Padrão para extrair elementos de um dicionário cujos valores são listas, alternando entre as chaves até que todas as listas estejam vazias.

## 📄 Implementação

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

## ⚙️ Como Funciona

1. **Dicionário `x`** contém chaves com listas de strings como valores.
2. **Loop infinito** (`while True`) itera até todas as listas estarem vazias.
3. A cada iteração, percorre todas as chaves e remove o primeiro elemento com `pop(0)`.
4. A flag `no_news_found` controla o encerramento — se nenhuma lista tiver elementos, o loop para.

## 📤 Saída Esperada

```
12
21
12
22
{'economia': [], 'investimento': []}
```

## 📝 Notas

- Funciona como um **"round-robin"** entre as chaves, processando um item de cada por vez.
- Útil para consumir filas de diferentes categorias de forma balanceada.

---
#python #dictionary #while #loop #algoritmos
