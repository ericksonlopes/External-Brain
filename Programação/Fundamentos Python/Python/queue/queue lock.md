```python
import queue
import threading
import time

teste_queue = queue.Queue()
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


for num in range(4):
    with processing_lock:
        teste_queue.put(num)

# Espera a fila ser processada
teste_queue.join()
print("Todos os itens foram processados.")

```