# Design Patterns e Padrões Arquiteturais em Python

Este guia compila diversos padrões de projeto e arquiteturas explicados com exemplos práticos em Python.

---

## 1. Padrões Arquiteturais

### 1.1. Monolítico
Centraliza todas as responsabilidades (coleta, processamento e exibição) em um único bloco ou arquivo.
```python
# Programa para cálculo de média de notas
notas = []
while True:
    nota = float(input("Digite uma nota (ou '0' para sair): "))
    if nota == 0:
        break
    notas.append(nota)

soma = sum(notas)
media = soma / len(notas)
print(f"A média das notas é: {media:.2f}")
```

### 1.2. Microsserviços
Divide o sistema em serviços independentes que se comunicam via rede (ex: HTTP/JSON).
```python
# auth_service.py (Exemplo com Flask)
from flask import Flask, request

app = Flask(__name__)

@app.route('/login', methods=['POST'])
def login():
    username = request.json.get('username')
    password = request.json.get('password')
    # Lógica de autenticação aqui...
    token = "token-gerado-123"
    return {'token': token}

if __name__ == '__main__':
    app.run(port=8080)
```

### 1.3. SOA (Service-Oriented Architecture)
Focado na disponibilização de serviços através de protocolos padronizados.
```python
# product_catalog_service.py
from flask import Flask, jsonify

app = Flask(__name__)
products = [{'id': 1, 'name': 'Produto 1', 'price': 10.0}]

@app.route('/products')
def get_products():
    return jsonify(products)

if __name__ == '__main__':
    app.run(port=8081)
```

### 1.4. Arquitetura em Camadas (Layered)
Separação clara entre Apresentação, Lógica de Negócio e Dados.
```python
# Camada de Dados
class InMemoryRepository:
    def __init__(self): self.tasks = []
    def add(self, task): self.tasks.append(task)

# Camada de Negócio
class TaskManager:
    def __init__(self, repo): self.repo = repo
    def add_task(self, desc): self.repo.add({'description': desc})

# Camada de Apresentação
def main():
    repo = InMemoryRepository()
    manager = TaskManager(repo)
    manager.add_task("Estudar Design Patterns")
```

### 1.5. Arquitetura Hexagonal (Ports and Adapters)
Isola o domínio central de tecnologias externas através de interfaces (Ports) e implementações (Adapters).
```python
# Port (Interface)
class TodoRepository:
    def save(self, todo): pass

# Adapter (Implementação)
class InMemoryTodoRepository(TodoRepository):
    def __init__(self): self.todos = {}
    def save(self, todo): self.todos[todo.id] = todo
```

---

## 2. Padrões Criacionais (Creational)

### 2.1. Abstract Factory
Fornece uma interface para criar famílias de objetos relacionados.
```python
from abc import ABC, abstractmethod

class AbstractPizzaFactory(ABC):
    @abstractmethod
    def create_dough(self): pass

class NYStylePizzaFactory(AbstractPizzaFactory):
    def create_dough(self): return "Thin Crust"
```

### 2.2. Builder
Permite a criação de objetos complexos passo a passo.
```python
class Car:
    def __init__(self):
        self.wheels = None
        self.engine = None

class CarBuilder:
    def build_wheels(self): return "Alloy"
    def get_car(self):
        car = Car()
        car.wheels = self.build_wheels()
        return car
```

### 2.3. Factory Method
Define uma interface para criar um objeto, mas deixa as subclasses decidirem qual classe instanciar.
```python
class AnimalCreator(ABC):
    @abstractmethod
    def create_animal(self): pass

class DogCreator(AnimalCreator):
    def create_animal(self): return Dog()
```

---

## 3. Padrões Estruturais (Structural)

### 3.1. Adapter
Permite que classes incompatíveis trabalhem juntas.
```python
class Adapter(Target):
    def __init__(self, adaptee):
        self.adaptee = adaptee
    def make_request(self):
        return self.adaptee.specific_request()
```

---

## 4. Padrões Comportamentais (Behavioral)

### 4.1. Command
Encapsula uma solicitação como um objeto.
```python
class LightOnCommand(Command):
    def __init__(self, light): self.light = light
    def execute(self): self.light.turn_on()
```

### 4.2. Observer
Define uma dependência um-para-muitos entre objetos para notificação de mudanças.
```python
class Subject:
    def __init__(self): self._observers = []
    def notify(self, message):
        for obs in self._observers: obs.update(message)
```

### 4.3. Strategy
Permite trocar o algoritmo em tempo de execução.
```python
class Context:
    def __init__(self, strategy): self._strategy = strategy
    def execute(self): return self._strategy.do_algorithm()
```

### 4.4. Iterator
Permite percorrer uma coleção sem expor sua estrutura interna.
```python
class MyIterator:
    def __next__(self):
        if self.index >= len(self.data): raise StopIteration
        # ... retorna dado
```

---
#python #design-patterns #arquitetura #desenvolvimento #gof #solid #clean-architecture