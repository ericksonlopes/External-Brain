# 🔒 Queue com Lock — Sincronização Produtor/Consumidor

Exemplo básico de fila com `threading.Lock` para sincronizar o acesso entre o produtor e o worker. O lock garante que o `put` e o `print` do processamento não se sobreponham.

## 📄 Implementação

```python
import queue
import threading
import time

teste_queue = queue.Queue()
processing_lock = threading.Lock()


def teste_worker():
    while True:
        item = teste_queue.get()
        with processing_lock:
            print(f"Item processado: {item}")
            time.sleep(1)
            teste_queue.task_done()


thread = threading.Thread(target=teste_worker, daemon=True)
thread.start()

for num in range(4):
    with processing_lock:
        teste_queue.put(num)

teste_queue.join()
print("Todos os itens foram processados.")
```

## 📝 Notas

- O `processing_lock` evita que o output do produtor e do consumidor se misturem no terminal.
- `teste_queue.join()` bloqueia até que todos os `task_done()` sejam chamados.
- Para uma versão com rastreio do item atual, veja [[queue tracking]].
- Para o básico de Queues, veja [[queue]].

---
#python #threads #concurrency #queue