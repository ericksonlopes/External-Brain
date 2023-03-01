O padrão Iterador (ou Iterator, em inglês) é um padrão de projeto de software comum em Python e outras linguagens de programação. Ele é usado para fornecer um meio de acessar elementos de uma coleção de objetos de maneira sequencial, sem expor a implementação da coleção subjacente.

Em resumo, o padrão Iterator separa a lógica de percorrer uma coleção de objetos em um objeto separado chamado iterador, para que a estrutura interna da coleção possa ser modificada sem afetar o código que a percorre.


# Vantagens
- **Abstração de implementação**: o padrão Iterador permite que você itere sobre os elementos de uma coleção sem precisar conhecer os detalhes de como a coleção é implementada internamente. Isso ajuda a abstrair a complexidade da implementação da coleção, tornando seu código mais simples e fácil de entender.

- **Reutilização de código**: uma vez que você implementou o padrão Iterador para uma coleção específica, você pode reutilizar o mesmo código para iterar sobre outras coleções que implementem o padrão Iterador. Isso pode economizar tempo e esforço na escrita de código.

- **Compatibilidade com Python**: o padrão Iterador é uma parte fundamental da linguagem Python e é amplamente usado em várias bibliotecas e frameworks. Usar o padrão Iterador em seu código pode ajudar a garantir que ele seja compatível com outras bibliotecas e frameworks em Python.

- **Iteração preguiçosa**: o padrão Iterador permite que você itere sobre elementos de uma coleção de forma preguiçosa. Isso significa que você pode iterar sobre uma coleção de maneira eficiente sem precisar carregar todos os elementos na memória de uma só vez. Em vez disso, os elementos são carregados sob demanda, conforme necessário.

- **Performance**: o padrão Iterador pode ser mais eficiente em termos de desempenho do que outras formas de iteração, especialmente para coleções grandes. Isso ocorre porque o padrão Iterador permite que você itere sobre os elementos de uma coleção sem precisar criar uma cópia da coleção ou armazenar todos os elementos na memória de uma só vez.
