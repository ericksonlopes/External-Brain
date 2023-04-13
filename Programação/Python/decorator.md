Em Python, um decorator é uma função que pode ser usada para modificar ou envolver outras funções ou classes. Os decorators são escritos antes da definição da função ou da classe e são indicados pelo símbolo "@".

Os decorators podem ser usados para adicionar funcionalidade às funções e classes, sem precisar alterar seu código. Eles podem ser usados para implementar recursos como:

-   Controle de acesso: o decorator pode verificar se o usuário tem permissão para acessar a função ou classe.
-   Logging: o decorator pode ser usado para registrar informações sobre o uso da função ou classe.
-   Cache: o decorator pode armazenar em cache os resultados de uma função para reduzir o tempo de processamento.
-   Medição de desempenho: o decorator pode ser usado para medir o tempo de execução de uma função.

Aqui está um exemplo simples de um decorator:

```python
def meu_decorator(func):
    def wrapper():
        print("Antes da função.")
        func()
        print("Depois da função.")
    return wrapper

@meu_decorator
def minha_funcao():
    print("Minha função.")

minha_funcao()
```

Nesse exemplo, o decorator `meu_decorator` envolve a função `minha_funcao` em uma nova função chamada `wrapper`. Quando a função `minha_funcao` é chamada, o decorator adiciona uma mensagem "Antes da função" antes da execução da função e outra mensagem "Depois da função" após a execução da função. O resultado da chamada `minha_funcao()` será:


```python
Antes da função.
Minha função.
Depois da função.
```

#decorator #