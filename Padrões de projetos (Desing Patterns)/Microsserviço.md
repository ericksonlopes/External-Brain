O padrão de projeto microsserviço é uma arquitetura que consiste em dividir uma aplicação em vários serviços
independentes, cada um executando uma função específica. Esses serviços podem ser desenvolvidos em diferentes linguagens
de programação e podem ser implantados e escalados independentemente.

Em Python, existem várias bibliotecas e estruturas de desenvolvimento de microserviços disponíveis, como Flask, Django,
FastAPI e Pyramid. Aqui estão alguns princípios e práticas recomendadas para projetar microserviços em Python:

## Vantagens

- **Escalabilidade**: com o uso de microserviços, é possível escalar e modificar a aplicação de forma granular e
  eficiente, sem afetar outras partes do sistema.

- **Flexibilidade**: microserviços são independentes e podem ser desenvolvidos, testados e implantados
  independentemente. Isso oferece mais flexibilidade e velocidade no desenvolvimento e implantação de novas
  funcionalidades.

- **Resiliência**: se um microserviço falhar, o restante da aplicação não é afetado, o que aumenta a resiliência da
  aplicação.

- **Fácil manutenção**: com os microserviços separados, é mais fácil manter e atualizar cada um deles individualmente,
  sem precisar lidar com a complexidade de uma aplicação monolítica.

- **Tecnologia independente**: com microserviços, é possível utilizar diferentes tecnologias para diferentes partes do
  sistema, escolhendo a mais adequada para cada necessidade.

## Desvantagens

- **Complexidade**: o uso de microserviços pode aumentar a complexidade da aplicação, principalmente quando há muitos
  microserviços interconectados.

- **Dificuldades de integração**: como os microserviços são independentes, a integração entre eles pode ser mais
  complexa e requerer mais tempo e esforço.

- **Overhead de rede**: como os microserviços são acessados através de uma rede, pode haver um overhead de rede maior do
  que em uma aplicação monolítica.

- **Gerenciamento de dados**: com microserviços independentes, pode ser necessário gerenciar dados e informações
  redundantes em diferentes serviços, o que pode ser mais complexo.

- **Mais desafios de segurança**: com mais serviços em execução, há mais superfície de ataque para possíveis
  vulnerabilidades, o que pode aumentar os desafios de segurança da aplicação.

## Exemplo

```python
