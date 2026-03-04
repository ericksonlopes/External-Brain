# 🧩 Agrupamento com Set — Union-Find Simplificado

Versão simplificada do padrão de agrupamento usando `set`. Busca um grupo existente que contenha um dos elementos e adiciona o novo; caso contrário, cria um grupo novo.

## 📄 Implementação

```python
groups = [{1, 2}, {3, 4}, {5, 6}]

i = 1
j = 7

found = False
for group in groups:
    if i in group or j in group:
        group.add(j)
        found = True
        break

if not found:
    groups.append({i, j})

print(groups)
# [{1, 2, 7}, {3, 4}, {5, 6}]
```

## ⚙️ Como Funciona

1. Percorre os grupos existentes procurando `i` ou `j`.
2. Se encontrar, adiciona o elemento ausente ao grupo.
3. Se nenhum grupo contiver `i` ou `j`, cria um novo grupo `{i, j}`.

## 📝 Notas

- Abordagem simples, mas **não lida com merge de grupos** — se `i` e `j` estiverem em grupos diferentes, apenas um é atualizado.
- Para uma solução completa com path compression, veja [[Union-Find com collections - Agrupamento por Conexões]].

---
#python #set #union-find