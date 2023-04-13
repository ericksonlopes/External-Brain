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

#datetime #python