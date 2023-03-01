#factory-method #design-patterns  #python 

O padrão Factory Method é um padrão de projeto de software que pertence à categoria de padrões de criação (também conhecidos como padrões de construção). Ele define uma interface para criar objetos, mas permite que as subclasses decidam qual classe instanciar. Ou seja, ele fornece um método abstrato para criar objetos, mas delega a escolha da classe concreta para as subclasses.

Em outras palavras, o padrão Factory Method é usado para encapsular a criação de objetos, permitindo que uma classe delegue a responsabilidade de criar objetos para suas subclasses. Isso pode ajudar a reduzir o acoplamento entre classes e fornecer uma maneira flexível de criar objetos.

# Vantagens 
- **Encapsulamento da criação de objetos**: O padrão Factory Method permite que a criação de objetos seja encapsulada em uma classe separada, o que ajuda a manter o código organizado e facilita a manutenção.

- **Flexibilidade**: O padrão Factory Method permite que as subclasses escolham a classe concreta a ser instanciada, o que oferece grande flexibilidade ao projeto. Isso significa que o código pode ser facilmente adaptado para atender às necessidades em constante evolução.

- **Redução do acoplamento**: Ao usar o padrão Factory Method, a classe que precisa de um objeto não precisa conhecer os detalhes de como o objeto é criado. Isso ajuda a reduzir o acoplamento entre classes, o que pode tornar o código mais fácil de entender, modificar e manter.

- **Reutilização de código**: O padrão Factory Method permite que as subclasses compartilhem código com a classe base. Isso pode ajudar a reduzir a duplicação de código e aumentar a reutilização de código.

- **Testabilidade**: O padrão Factory Method pode ajudar a tornar o código mais testável, pois permite que os objetos sejam substituídos por objetos falsos ou de teste. Isso pode ser útil ao escrever testes automatizados para garantir que o código esteja funcionando corretamente.

# Desvantagens