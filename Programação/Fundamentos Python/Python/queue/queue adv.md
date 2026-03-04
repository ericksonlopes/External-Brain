# 🏗️ QueueProcessor — Processador de Fila Orientado a Objetos

Evolução do padrão produtor/consumidor encapsulado em classes. Combina `TrackingQueue` com um `QueueProcessor` que gerencia a thread worker internamente.

## 📄 Implementação

```python
import threading
import time
from queue import Queue, Empty


class TrackingQueue(Queue):
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


class QueueProcessor:
    """Encapsula a thread worker e o lock de processamento."""

    def __init__(self, queue):
        self.queue = queue
        self.lock = threading.Lock()
        self.thread = threading.Thread(target=self.worker, daemon=True)

    def start(self):
        self.thread.start()

    def worker(self):
        while True:
            try:
                item = self.queue.get(timeout=1)
                with self.lock:
                    print(f"Item processado: {item}")
                    time.sleep(1)
                    self.queue.task_done()
            except Empty:
                continue
```

## 🚀 Exemplo de Uso

```python
def main():
    teste_queue = TrackingQueue()
    processor = QueueProcessor(teste_queue)
    processor.start()

    try:
        while True:
            item = input("Digite um item (ou 'sair' para finalizar): ")
            if item.lower() == 'sair':
                break

            with processor.lock:
                teste_queue.put(item)
                print(f"Item '{item}' adicionado à fila.")
                print(f"Item atual em processamento: {teste_queue.current_task()}")

            teste_queue.join()
    except KeyboardInterrupt:
        print("\nProcesso interrompido pelo usuário.")
    finally:
        print("Todos os itens foram processados.")

if __name__ == "__main__":
    main()
```

## 📝 Notas

- Diferente do [[queue tracking]], aqui o worker usa `get(timeout=1)` com tratamento de `Empty`, evitando bloqueio permanente.
- O `QueueProcessor` encapsula thread + lock, tornando o código mais reutilizável.
- Para o básico de Queues, veja [[queue]].

---
#python #threads #concurrency #queue #oop