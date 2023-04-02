```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("Python Spark SQL basic example").getOrCreate()

schema = "Produto String, Quantidade Integer"
dados = [("Caneta", 20), ("Tesoura", 30), ("Caneta", 40)]

df = spark.createDataFrame(data=dados, schema=schema)
```

# Visualizando dados

```python
df.show()
```
Saída:

    +-------+----------+
    |Produto|Quantidade|
    +-------+----------+
    | Caneta|        20|
    |Tesoura|        30|
    | Caneta|        40|
    +-------+----------+

# Selecionando colunas

```python
df.select("Produto").show()
```
Saída:

    +-------+
    |Produto|
    +-------+
    | Caneta|
    |Tesoura|
    | Caneta|
    +-------+

# Filtrando dados

```python
df.filter(df["Quantidade"] > 20).show()
```
Saída:

    +-------+----------+
    |Produto|Quantidade|
    +-------+----------+
    |Tesoura|        30|
    | Caneta|        40|
    +-------+----------+

```python
df.show()
```
Saída:

    +-------+----------+
    |Produto|Quantidade|
    +-------+----------+
    | Caneta|        20|
    |Tesoura|        30|
    | Caneta|        40|
    +-------+----------+

# Agrupando dados

```python
df.groupBy("Produto").sum().show()
```
Saída:

    +-------+---------------+
    |Produto|sum(Quantidade)|
    +-------+---------------+
    | Caneta|             60|
    |Tesoura|             30|
    +-------+---------------+

```python
from pyspark.sql.functions import sum

df.groupBy("Produto").agg(sum("Quantidade")).show()
```
Saída:

    [Stage 27:===================>                                      (2 + 4) / 6]

    +-------+---------------+
    |Produto|sum(Quantidade)|
    +-------+---------------+
    | Caneta|             60|
    |Tesoura|             30|
    +-------+---------------+

# Aplicando expressões

```python
from pyspark.sql.functions import expr

df.select("Produto", "Quantidade", expr("Quantidade * 0.2 as Imposto")).show()
```
Saída:

    +-------+----------+-------+
    |Produto|Quantidade|Imposto|
    +-------+----------+-------+
    | Caneta|        20|    4.0|
    |Tesoura|        30|    6.0|
    | Caneta|        40|    8.0|
    +-------+----------+-------+
