```python
import queue
import threading
import time

class TrackingQueue(queue.Queue):

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


# Inicialização dos recursos
teste_queue = TrackingQueue()
processing_lock = threading.Lock()

def teste_worker():
    while True:
        item = teste_queue.get()  # Bloqueia até obter um item
        with processing_lock:
            print(f"Item processado: {item}")
            time.sleep(1)  # Simulando o tempo de processamento
            teste_queue.task_done()

# Inicia a thread worker
thread = threading.Thread(target=teste_worker, daemon=True)
thread.start()

# Loop para adicionar itens à fila a partir da entrada do usuário
while True:
    item = input("Digite um item para adicionar à fila (ou 'sair' para finalizar): ")
    if item.lower() == 'sair':
        break
    
    with processing_lock:
        teste_queue.put(item)
        print(f"Item '{item}' adicionado à fila.")
        print(f"Item atual em processamento: {teste_queue.current_task()}")

    teste_queue.join()  # Espera a fila ser processada antes de aceitar o próximo item

print("Todos os itens foram processados.")

```