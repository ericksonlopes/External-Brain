# Entendendo @classmethod e @staticmethod em Python

Em Python, além dos métodos de instância comuns (que recebem `self`), temos dois decoradores importantes para definir comportamentos específicos em classes: `@classmethod` e `@staticmethod`.

---

## 1. @classmethod (Método de Classe)

Um método de classe recebe a própria **classe** como primeiro argumento (por convenção, chamado de `cls`), em vez de uma instância.

### Quando usar?
- Quando você precisa acessar ou modificar o estado da classe (atributos de classe).
- Para criar **Factory Methods** (métodos que retornam uma nova instância da classe de formas diferentes).

### Exemplo Simples
```python
class Usuario:
    plataforma = "Web"

    def __init__(self, nome):
        self.nome = nome

    @classmethod
    def mudar_plataforma(cls, nova_plataforma):
        cls.plataforma = nova_plataforma

    @classmethod
    def criar_anonimo(cls):
        return cls("Anônimo")

# Uso
Usuario.mudar_plataforma("Mobile")
user1 = Usuario.criar_anonimo()

print(user1.nome)       # Saída: Anônimo
print(user1.plataforma) # Saída: Mobile
```

---

## 2. @staticmethod (Método Estático)

Um método estático não recebe nem a classe (`cls`) nem a instância (`self`) como argumento automático. Ele se comporta como uma função comum, mas reside dentro do namespace da classe.

### Quando usar?
- Quando o método não precisa acessar nada da classe ou da instância.
- Para organizar funções utilitárias que têm uma ligação lógica com a classe, mas são independentes de seu estado.

### Exemplo Simples
```python
class Calculadora:
    @staticmethod
    def validar_numero(n):
        return isinstance(n, (int, float))

    @staticmethod
    def somar(a, b):
        return a + b

# Uso
print(Calculadora.validar_numero(10)) # Saída: True
print(Calculadora.somar(5, 5))         # Saída: 10
```

---

## 3. Comparativo Rápido

| Característica | Método de Instância | @classmethod | @staticmethod |
| :--- | :--- | :--- | :--- |
| **Primeiro Argumento** | `self` (instância) | `cls` (classe) | Nenhum |
| **Acesso à Instância** | Sim | Não | Não |
| **Acesso à Classe** | Sim | Sim | Não |
| **Uso Comum** | Comportamento do objeto | Factories / Estado global | Utilitários / Lógica pura |

---

## Resumo Visual
- **Instância (`self`)**: "Eu sei quem eu sou e o que eu tenho."
- **Classe (`cls`)**: "Eu sei qual é o meu molde (classe) e posso criar novos como eu."
- **Estático**: "Eu apenas executo uma tarefa lógica relacionada a este tema, mas não preciso saber de nada interno."
