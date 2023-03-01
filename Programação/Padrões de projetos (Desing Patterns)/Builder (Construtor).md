O padrão Builder é um padrão de projeto de software que permite criar objetos complexos passo a passo. Ele separa a construção de um objeto de sua representação final, permitindo que diferentes tipos de objetos sejam criados a partir do mesmo processo de construção.

Em Python, o padrão Builder pode ser implementado utilizando classes e métodos. Geralmente, a implementação envolve a criação de uma classe Builder que é responsável por construir o objeto, e uma classe Produto que representa o objeto que está sendo construído. A classe Builder define uma interface para criar os diferentes componentes do objeto, enquanto a classe Produto é a classe que é construída passo a passo.

Ao utilizar o padrão Builder, é possível criar objetos complexos sem precisar passar um grande número de parâmetros para o construtor. Além disso, o padrão torna o código mais flexível, permitindo a criação de diferentes tipos de objetos com base no mesmo processo de construção.

# Vantagens
- **Separação da construção do objeto de sua representação final**: Isso permite que diferentes tipos de objetos sejam criados a partir do mesmo processo de construção. Além disso, a separação da construção da representação final torna o código mais flexível e fácil de manter.

- **Encapsulamento**: O padrão Builder encapsula o processo de construção do objeto em uma única classe, o que ajuda a reduzir a complexidade do código.

- **Controle do processo de construção**: O padrão Builder permite controlar o processo de construção passo a passo. Isso significa que é possível criar objetos complexos sem precisar passar um grande número de parâmetros para o construtor.

- **Flexibilidade**: Com o padrão Builder, é possível criar diferentes tipos de objetos com base no mesmo processo de construção. Isso é especialmente útil quando se deseja criar objetos que possuem configurações variáveis.

- **Reutilização de código**: O padrão Builder permite reutilizar o mesmo processo de construção para criar diferentes objetos. Isso ajuda a reduzir a duplicação de código e torna o código mais fácil de manter.

# Desvantagens

# Exemplo
