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

```python
from abc import ABC, abstractmethod

class CarFactory(ABC):
    @abstractmethod
    def create_sports_car(self):
        pass
    
    @abstractmethod
    def create_sedan(self):
        pass
        
class SportsCarFactory(CarFactory):
    def create_sports_car(self):
        return SportsCar()
    
    def create_sedan(self):
        return LuxurySedan()
        
class FamilyCarFactory(CarFactory):
    def create_sports_car(self):
        return BasicCar()
    
    def create_sedan(self):
        return FamilySedan()
        
if __name__ == '__main__':
    # Cria uma fábrica de carros esportivos
    factory = SportsCarFactory()
    
    # Cria um carro esportivo
    sports_car = factory.create_sports_car()
    
    # Cria um sedan de luxo
    sedan = factory.create_sedan()
```
