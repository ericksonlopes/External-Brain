# 🌳 Union-Find com collections — Agrupamento por Conexões

Implementação do algoritmo **Union-Find** (Disjoint Set) usando `defaultdict` para agrupar elementos conectados. Utiliza **path compression** no `find` para otimizar buscas.

## 📄 Implementação

```python
from collections import defaultdict


class UnionFind:
    def __init__(self, documents):
        self.parents = {doc_id: doc_id for doc_id in documents}

    def find(self, doc_id):
        if self.parents[doc_id] != doc_id:
            self.parents[doc_id] = self.find(self.parents[doc_id])  # Path compression
        return self.parents[doc_id]

    def union(self, doc_id1, doc_id2):
        root1 = self.find(doc_id1)
        root2 = self.find(doc_id2)
        if root1 != root2:
            self.parents[root1] = root2

    def display_groups(self):
        groups = defaultdict(list)
        for doc_id in self.parents.keys():
            root = self.find(doc_id)
            groups[root].append(doc_id)

        for root, group_members in groups.items():
            print(f"Grupo {root}: {group_members}")
```

## 🚀 Exemplo de Uso

```python
documents = [1, 2, 3, 4, 5, 7]
uf = UnionFind(documents)

uf.union(1, 2)
uf.union(1, 7)
uf.union(2, 3)
uf.union(4, 5)

uf.display_groups()
```

## 📤 Saída Esperada

```
Grupo 3: [1, 2, 3, 7]
Grupo 5: [4, 5]
```

## 📝 Notas

- **Path compression** no `find` achata a árvore, tornando buscas futuras O(1) amortizado.
- Para uma versão simplificada usando `set`, veja [[Agrupamento com Set - Union-Find Simplificado]].

---
#python #collections #union-find