O padrão Abstract Factory é um padrão de design que fornece uma interface para criar famílias de objetos relacionados ou
dependentes sem especificar as suas classes concretas.

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
  criação de objetos complexos é encapsulada numa classe específica e pode não ser fácil de reutilizar em outros
  contextos.

## Exemplo

Neste exemplo, vamos criar uma classe abstrata Car, que será a classe base para as classes concretas SportsCar e
CommonCar. A classe abstrata Car possui dois métodos abstratos, drive e stop, que serão implementados pelas classes
concretas.

```python
from abc import ABC, abstractmethod


# Classe abstrata
class Car(ABC):
    def __init__(self, name):
        self.name = name

    @abstractmethod
    def drive(self):
        pass

    @abstractmethod
    def stop(self):
        pass


# Classe concreta
class SportsCar(Car):
    def drive(self):
        return f"{self.name} esta dirigindo muito rápido!"

    def stop(self):
        return f"{self.name} esta parando muito rápido!"


# Classe concreta
class CommonCar(Car):
    def drive(self):
        return f"{self.name} esta dirigindo em velocidade normal."

    def stop(self):
        return f"{self.name} está parando em velocidade normal."


# Criação de instâncias de carros
sport_car = SportsCar("Ferrari")
truck = CommonCar("Uno")

# Execução dos métodos
print(sport_car.drive())
print(sport_car.stop())

print(truck.drive())
print(truck.stop())

```

#design-pattern #factory-abstract #python
