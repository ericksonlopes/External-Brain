#set #python #groups #Union-Find #Union-by-Rank
```python
groups = [{1, 2}, {3, 4}, {5, 6}]

found = False

i = 1
j = 7

for group in groups:
    if i in group or j in group:
        group.add(j)
        found = True
        break
if not found:
    groups.append({i, j})

print(groups)

```