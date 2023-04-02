#pypsark #python

## Criando um dataframe

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName('Basics').getOrCreate()

df = spark.createDataFrame([(0, "Hello"), (1, "World")], ["id", "word"])

df.show()
```

## Criando data frame com schema

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName('Basics').getOrCreate()

shema = "id INT, word STRING"
data = [(0, "Hello"), (1, "World")]

df = spark.createDataFrame(data, shema)

df.show()
```

## Selecionando colunas

```python
df.select("word", "id").show()
```

## Filtrando

```python
df.filter(df.id == 0).show()
```

