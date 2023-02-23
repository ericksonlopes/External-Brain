Um padrão de projeto monolítico em Python é um estilo de arquitetura de software em que toda a aplicação é desenvolvida como um único bloco de código que é executado em um único processo. Neste modelo, todas as funcionalidades da aplicação são desenvolvidas e implementadas dentro do mesmo código.

## Vantagens

O padrão monolítico em Python geralmente é usado em pequenas e médias aplicações em que a complexidade do código não é muito alta e a escalabilidade não é uma preocupação imediata.

Alguns dos benefícios do padrão monolítico em Python incluem:

- Simplicidade: Como toda a aplicação está contida em um único código, é fácil entender o funcionamento da aplicação como um todo.

- Facilidade de desenvolvimento: Com a aplicação inteira em um único código, é fácil para um desenvolvedor trabalhar em todas as funcionalidades da aplicação.

- Menor tempo de desenvolvimento: Com a simplicidade e facilidade de desenvolvimento, a aplicação pode ser desenvolvida rapidamente.

## Desvantagens

Embora o padrão monolítico em Python seja simples e fácil de desenvolver, pode apresentar algumas limitações em relação à escalabilidade e manutenção, especialmente em aplicações maiores e mais complexas. Por isso, é importante avaliar cuidadosamente as necessidades da sua aplicação antes de escolher o padrão de projeto a ser utilizado.

# Exemplo

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
print("A média das notas é: {:.2f}".format(media))
```

Nesse exemplo, todas as etapas do programa - coleta das notas, cálculo da média e exibição do resultado - estão dentro do mesmo arquivo e dentro de um único bloco de código. Não há separação clara de funções ou módulos, o que pode dificultar a manutenção e o teste do código no futuro.

#python #design-pattern #Padrão-de-projeto #monolitico