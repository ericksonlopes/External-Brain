# 🔍 TrackingQueue — Fila com Rastreio do Item Atual

Extensão do `queue.Queue` que rastreia qual item está sendo processado no momento. Útil para monitoramento e debugging de pipelines multithreaded.

## 📄 Implementação

```python
import queue
import threading
import time


class TrackingQueue(queue.Queue):
    """Queue que expõe o item atualmente em processamento."""

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.current_item = None

    def get(self, *args, **kwargs):
        self.current_item = super().get(*args, **kwargs)
        return self.current_item

    def task_done(self):
        super().task_done()
        self.current_item = None

    def current_task(self):
        return self.current_item
```

## 🚀 Exemplo de Uso

Produtor interativo via terminal com worker em thread separada:

```python
teste_queue = TrackingQueue()
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

while True:
    item = input("Digite um item para adicionar à fila (ou 'sair' para finalizar): ")
    if item.lower() == 'sair':
        break

    with processing_lock:
        teste_queue.put(item)
        print(f"Item '{item}' adicionado à fila.")
        print(f"Item atual em processamento: {teste_queue.current_task()}")

    teste_queue.join()

print("Todos os itens foram processados.")
```

## 📝 Notas

- O `processing_lock` garante que o `put` e a leitura do `current_task()` não conflitem com o `get` do worker.
- A thread é `daemon=True`, então encerra automaticamente quando o programa principal termina.
- Para o básico de Queues, veja [[📖 queue]].

---
#python #threads #concurrency #queue