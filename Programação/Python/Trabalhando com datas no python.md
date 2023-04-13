Python tem uma biblioteca padrão chamada `datetime` para trabalhar com datas e horas. Esta biblioteca inclui classes para representar datas, horários, intervalos de tempo e combinações de datas e horários.

Aqui estão alguns exemplos básicos de como trabalhar com datas em Python usando a biblioteca `datetime`:

```python
import datetime

# Criando uma data
data_atual = datetime.date.today()
print(data_atual)

# Criando uma data específica
data_especifica = datetime.date(2022, 8, 17)
print(data_especifica)

# Obtendo o dia, mês e ano de uma data
print(data_atual.day)
print(data_atual.month)
print(data_atual.year)

# Formatando uma data como string
print(data_atual.strftime('%d/%m/%Y'))

# Realizando operações com datas
data_futura = data_atual + datetime.timedelta(days=10)
print(data_futura)
diferenca_datas = data_futura - data_atual
print(diferenca_datas.days)
```

Para transformar uma string em um objeto datetime, podemos usar o método strptime() da classe datetime.datetime. Este método converte uma string em um objeto datetime com base em um formato específico.

Por exemplo, suponha que temos uma string contendo uma data no formato dd/mm/aaaa:

```python
data_string = '15/06/2022'
```
Para converter esta string em um objeto datetime, podemos fazer o seguinte:

```python
import datetime

data_datetime = datetime.datetime.strptime(data_string, '%d/%m/%Y')
print(data_datetime)
```

#datetime #python