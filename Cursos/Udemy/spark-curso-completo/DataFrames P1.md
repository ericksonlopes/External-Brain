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

  

    +-------+----------+

    |Produto|Quantidade|

    +-------+----------+

    | Caneta|        20|

    |Tesoura|        30|

    | Caneta|        40|

    +-------+----------+

  

# Selecionando colunas

  
  

```python

df.select("Produto").show()

```

  

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

  

    +-------+----------+

    |Produto|Quantidade|

    +-------+----------+

    |Tesoura|        30|

    | Caneta|        40|

    +-------+----------+

  

# Agrupando dados p/1

  
  

```python

df.groupBy("Produto").sum().show()

```

  

    +-------+---------------+

    |Produto|sum(Quantidade)|

    +-------+---------------+

    | Caneta|             60|

    |Tesoura|             30|

    +-------+---------------+

  

# Agrupando dados p/2

  
  

```python

from pyspark.sql.functions import sum

df.groupBy("Produto").agg(sum("Quantidade")).show()

```

  

    +-------+---------------+

    |Produto|sum(Quantidade)|

    +-------+---------------+

    | Caneta|             60|

    |Tesoura|             30|

    +-------+---------------+

  

# Aplicando expressões

  
  

```python

from pyspark.sql.functions import expr

  

df.select("Produto", "Quantidade", expr("Quantidade * 0.2 as Imposto")).show()

```

  

    +-------+----------+-------+

    |Produto|Quantidade|Imposto|

    +-------+----------+-------+

    | Caneta|        20|    4.0|

    |Tesoura|        30|    6.0|

    | Caneta|        40|    8.0|

    +-------+----------+-------+

  

# Visualizando o schema

  
  

```python

df.schema

```

  
  
  
  

    StructType([StructField('Produto', StringType(), True), StructField('Quantidade', IntegerType(), True)])