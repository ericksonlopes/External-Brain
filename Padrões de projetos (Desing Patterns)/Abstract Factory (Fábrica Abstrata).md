O padrão Abstract Factory é um padrão de design que fornece uma interface para criar famílias de objetos relacionados ou
dependentes sem especificar suas classes concretas.

## Vantagens

- Separação de responsabilidades: O Abstract Factory promove a separação de responsabilidades, permitindo que a criação
  de objetos seja tratada por uma classe separada.

- Encapsulamento: O padrão Abstract Factory encapsula a criação de objetos complexos, tornando a criação de objetos
  mais fácil e menos propensa a erros.

- Flexibilidade: O Abstract Factory permite a criação de famílias de objetos relacionados sem ter que se preocupar com
  as classes concretas específicas.

- Facilidade de manutenção: O Abstract Factory torna a manutenção do código mais fácil, já que o código é mais
  organizado e as alterações podem ser feitas sem afetar o restante do sistema.

## Desvantagens

- Complexidade: A implementação do padrão Abstract Factory pode ser complexa, pois requer a criação de várias classes e
  interfaces.

- Acoplamento: O Abstract Factory pode aumentar o acoplamento entre classes, tornando o sistema mais difícil de manter e
  alterar.

- Sobrecarga de código: A implementação do Abstract Factory pode levar a uma sobrecarga de código, com muitas classes e
  interfaces sendo criadas, o que pode tornar o código mais difícil de entender.

- Limitações de reutilização: O Abstract Factory pode ter limitações em relação à reutilização de código, já que a
  criação de objetos complexos é encapsulada em uma classe específica e pode não ser fácil de reutilizar em outros
  contextos.

## Exemplo

```python
from abc import ABC, abstractmethod

# Definição da classe abstrata AnimalFactory
class AnimalFactory(ABC):
    @abstractmethod
    def create_dog(self):
        pass
    
    @abstractmethod
    def create_cat(self):
        pass
    
    @abstractmethod
    def create_bird(self):
        pass

# Definição das classes concretas de fábricas de animais
class WildAnimalFactory(AnimalFactory):
    def create_dog(self):
        return Wolf()
    
    def create_cat(self):
        return Lion()
    
    def create_bird(self):
        return Eagle()

class PetAnimalFactory(AnimalFactory):
    def create_dog(self):
        return Dog()
    
    def create_cat(self):
        return Cat()
    
    def create_bird(self):
        return Parrot()

# Definição das classes concretas de animais
class Dog:
    def speak(self):
        return "Woof!"

class Cat:
    def speak(self):
        return "Meow!"

class Parrot:
    def speak(self):
        return "Squawk!"

class Wolf:
    def speak(self):
        return "Howl!"

class Lion:
    def speak(self):
        return "Roar!"

class Eagle:
    def speak(self):
        return "Screech!"

# Exemplo de uso
wild_factory = WildAnimalFactory()
pet_factory = PetAnimalFactory()

wild_dog = wild_factory.create_dog()
wild_cat = wild_factory.create_cat()
wild_bird = wild_factory.create_bird()

pet_dog = pet_factory.create_dog()
pet_cat = pet_factory.create_cat()
pet_bird = pet_factory.create_bird()

print(wild_dog.speak())
print(wild_cat.speak())
print(wild_bird.speak())

print(pet_dog.speak())
print(pet_cat.speak())
print(pet_bird.speak())
```
