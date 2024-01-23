#collections #python #groups #Union-Find #Union-by-Rank

```python
from collections import defaultdict


class UnionFind:
    def __init__(self, documents):
        self.parents = {doc_id: doc_id for doc_id in documents}

    def find(self, doc_id):
        if self.parents[doc_id] != doc_id:
            self.parents[doc_id] = self.find(self.parents[doc_id])
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


# Exemplo de uso
documents = [1, 2, 3, 4, 5, 7]
uf = UnionFind(documents)

uf.union(1, 2)
uf.union(1, 7)
uf.union(2, 3)
uf.union(4, 5)

uf.display_groups()
```

```text
Grupo 3: [1, 2, 3, 7]
Grupo 5: [4, 5]
```

