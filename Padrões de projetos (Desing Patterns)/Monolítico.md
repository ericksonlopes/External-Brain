Um padrão de projeto monolítico em Python é um estilo de arquitetura de software em que toda a aplicação é desenvolvida como um único bloco de código que é executado em um único processo. Neste modelo, todas as funcionalidades da aplicação são desenvolvidas e implementadas dentro do mesmo código.

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