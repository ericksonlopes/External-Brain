# 🧵 Processamento Paralelo com Queues em Python

As **Queues (Filas)** são fundamentais para o desenvolvimento de aplicações multithreaded seguras. Elas permitem que diferentes partes do seu código (threads) troquem dados sem causar condições de corrida (race conditions).

## 🛠️ Como Funciona o `queue.Queue`

O módulo `queue` implementa filas multi-produtor e multi-consumidor. Ele é especialmente útil para passar informações entre threads de forma segura.

### Exemplo: Produtor e Consumidor

```python
import queue
import threading
import time

# 1. Criar a Fila
q = queue.Queue()

# 2. Função do Trabalhador (Consumidor)
def worker():
    while True:
        item = q.get() # Espera um item chegar
        if item is None: # Sinal para parar
            break
        print(f'Processando item: {item}')
        time.sleep(1) # Simula trabalho pesado
        q.task_done() # Notifica que o trabalho acabou

# 3. Iniciar a Thread
t = threading.Thread(target=worker)
t.start()

# 4. Adicionar Itens (Produtor)
for item in range(5):
    q.put(item)

# 5. Sincronização
q.join()       # Bloqueia até que task_done() seja chamado para todos os itens
q.put(None)    # Envia o sinal de parada para a thread
t.join()       # Espera a thread terminar
```

## 💡 Quando usar?

- **Web Scrapers**: Uma thread busca URLs e outras processam o HTML.
- **Processamento de Logs**: Threads de aplicação colocam logs na fila e uma thread dedicada salva no disco/banco.
- **Pipelines de Dados**: Mover dados entre estágios de transformação de forma assíncrona.

---
#python #threads #concurrency #backend #queue
