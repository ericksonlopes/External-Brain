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

Nesse exemplo, temos as interfaces abstratas `Car` e `CarFactory`. As classes concretas `SportsCar` e `CommonCar` implementam a interface `Car`, enquanto as classes `CarFactoryBrazil`, `SportsCarBrazil` e `CommonCarBrazil` implementam a interface `CarFactory` e a interface `Car` para criar objetos relacionados. A fábrica concreta `CarFactoryBrazil` cria carros brasileiros e as classes `SportsCarBrazil` e `CommonCarBrazil` implementam as operações específicas para esses carros.

Ao criar uma instância da fábrica `CarFactoryBrazil`, podemos criar instâncias de carros brasileiros por meio dos métodos `create_sports_car()` e `create_common_car()`. Dessa forma, conseguimos criar objetos relacionados de maneira independente da sua implementação concreta.

```python
from abc import ABC, abstractmethod

# Interface abstrata para os carros
class Car(ABC):
    @abstractmethod
    def drive(self):
        pass

    @abstractmethod
    def stop(self):
        pass


# Interface abstrata para a fábrica de carros
class CarFactory(ABC):
    @abstractmethod
    def create_sports_car(self):
        pass

    @abstractmethod
    def create_common_car(self):
        pass


# Classe concreta para o carro esportivo
class SportsCar(Car):
    def drive(self):
        return "Dirigindo o carro esportivo em alta velocidade."

    def stop(self):
        return "Parando o carro esportivo rapidamente."


# Classe concreta para o carro comum
class CommonCar(Car):
    def drive(self):
        return "Dirigindo o carro comum em velocidade normal."

    def stop(self):
        return "Parando o carro comum em velocidade normal."


# Fábrica concreta para os carros
class CarFactoryBrazil(CarFactory):
    def create_sports_car(self):
        return SportsCarBrazil()

    def create_common_car(self):
        return CommonCarBrazil()


# Fábrica concreta para os carros brasileiros
class SportsCarBrazil(SportsCar):
    def drive(self):
        return "Dirigindo o carro esportivo brasileiro em alta velocidade."

    def stop(self):
        return "Parando o carro esportivo brasileiro rapidamente."


# Fábrica concreta para os carros brasileiros
class CommonCarBrazil(CommonCar):
    def drive(self):
        return "Dirigindo o carro comum brasileiro em velocidade normal."

    def stop(self):
        return "Parando o carro comum brasileiro em velocidade normal."


# Criação de instância da fábrica de carros
factory = CarFactoryBrazil()

# Criação de instâncias de carros
sports_car = factory.create_sports_car()
common_car = factory.create_common_car()

# Execução dos métodos
print(sports_car.drive())
print(sports_car.stop())

print(common_car.drive())
print(common_car.stop())

```

#design-patterns #factory-abstract #python
