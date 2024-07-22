```python
import threading
import time
from queue import Queue, Empty

class TrackingQueue(Queue):
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
    def __init__(self, queue):
        self.queue = queue
        self.lock = threading.Lock()
        self.thread = threading.Thread(target=self.worker, daemon=True)

    def start(self):
        self.thread.start()

    def worker(self):
        while True:
            try:
                item = self.queue.get(timeout=1)  # Espera por um item por até 1 segundo
                with self.lock:
                    print(f"Item processado: {item}")
                    time.sleep(1)  # Simulando o tempo de processamento
                    self.queue.task_done()
            except Empty:
                continue  # Se a fila estiver vazia, continua no loop

def main():
    teste_queue = TrackingQueue()
    processor = QueueProcessor(teste_queue)
    processor.start()

    # Loop para adicionar itens à fila a partir da entrada do usuário
    try:
        while True:
            item = input("Digite um item para adicionar à fila (ou 'sair' para finalizar): ")
            if item.lower() == 'sair':
                break

            with processor.lock:
                teste_queue.put(item)
                print(f"Item '{item}' adicionado à fila.")
                print(f"Item atual em processamento: {teste_queue.current_task()}")

            teste_queue.join()  # Espera a fila ser processada antes de aceitar o próximo item
    except KeyboardInterrupt:
        print("\nProcesso interrompido pelo usuário.")
    finally:
        print("Todos os itens foram processados.")

if __name__ == "__main__":
    main()

```