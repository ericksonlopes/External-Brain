```python
  from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("dados").getOrCreate()
```

# Definindo schema do arquivo manualmente


```python
arqschema = "id int, nome string, status string, cidade string, vendas int, data string"

despachantes = spark.read.csv("arquivos/despachantes.csv", header=False, schema=arqschema)

despachantes.show()
```

    +---+-------------------+------+-------------+------+----------+
    | id|               nome|status|       cidade|vendas|      data|
    +---+-------------------+------+-------------+------+----------+
    |  1|   Carminda Pestana| Ativo|  Santa Maria|    23|2020-08-11|
    |  2|    Deolinda Vilela| Ativo|Novo Hamburgo|    34|2020-03-05|
    |  3|   Emídio Dornelles| Ativo| Porto Alegre|    34|2020-02-05|
    |  4|Felisbela Dornelles| Ativo| Porto Alegre|    36|2020-02-05|
    |  5|     Graça Ornellas| Ativo| Porto Alegre|    12|2020-02-05|
    |  6|   Matilde Rebouças| Ativo| Porto Alegre|    22|2019-01-05|
    |  7|    Noêmia   Orriça| Ativo|  Santa Maria|    45|2019-10-05|
    |  8|      Roque Vásquez| Ativo| Porto Alegre|    65|2020-03-05|
    |  9|      Uriel Queiroz| Ativo| Porto Alegre|    54|2018-05-05|
    | 10|   Viviana Sequeira| Ativo| Porto Alegre|     0|2020-09-05|
    +---+-------------------+------+-------------+------+----------+



# Ler arquivo e criar schema automaticamente


```python
despachantes_autoschema = spark.read.load("arquivos/despachantes.csv", header=False, format="csv", sep=",", inferSchema=True)

despachantes_autoschema.show()
```

    +---+-------------------+-----+-------------+---+-------------------+
    |_c0|                _c1|  _c2|          _c3|_c4|                _c5|
    +---+-------------------+-----+-------------+---+-------------------+
    |  1|   Carminda Pestana|Ativo|  Santa Maria| 23|2020-08-11 00:00:00|
    |  2|    Deolinda Vilela|Ativo|Novo Hamburgo| 34|2020-03-05 00:00:00|
    |  3|   Emídio Dornelles|Ativo| Porto Alegre| 34|2020-02-05 00:00:00|
    |  4|Felisbela Dornelles|Ativo| Porto Alegre| 36|2020-02-05 00:00:00|
    |  5|     Graça Ornellas|Ativo| Porto Alegre| 12|2020-02-05 00:00:00|
    |  6|   Matilde Rebouças|Ativo| Porto Alegre| 22|2019-01-05 00:00:00|
    |  7|    Noêmia   Orriça|Ativo|  Santa Maria| 45|2019-10-05 00:00:00|
    |  8|      Roque Vásquez|Ativo| Porto Alegre| 65|2020-03-05 00:00:00|
    |  9|      Uriel Queiroz|Ativo| Porto Alegre| 54|2018-05-05 00:00:00|
    | 10|   Viviana Sequeira|Ativo| Porto Alegre|  0|2020-09-05 00:00:00|
    +---+-------------------+-----+-------------+---+-------------------+



# Visualizar schemas


```python
despachantes_autoschema.schema
```




    StructType([StructField('_c0', IntegerType(), True), StructField('_c1', StringType(), True), StructField('_c2', StringType(), True), StructField('_c3', StringType(), True), StructField('_c4', IntegerType(), True), StructField('_c5', TimestampType(), True)])




```python
despachantes.schema
```




    StructType([StructField('id', IntegerType(), True), StructField('nome', StringType(), True), StructField('status', StringType(), True), StructField('cidade', StringType(), True), StructField('vendas', IntegerType(), True), StructField('data', StringType(), True)])



# Filtrando dados

Verificando os valores maiores que 30 vendas


```python
from pyspark.sql import functions

cond = functions.col("vendas") > 30
despachantes.select("id", "nome", "vendas").where(cond).show()
```

    +---+-------------------+------+
    | id|               nome|vendas|
    +---+-------------------+------+
    |  2|    Deolinda Vilela|    34|
    |  3|   Emídio Dornelles|    34|
    |  4|Felisbela Dornelles|    36|
    |  7|    Noêmia   Orriça|    45|
    |  8|      Roque Vásquez|    65|
    |  9|      Uriel Queiroz|    54|
    +---+-------------------+------+




Verificando valores maiores que 20 e menores que 30


```python
cond = (functions.col("vendas") > 20) & (functions.col("vendas") < 40)

despachantes.select("nome", "vendas").where(cond).show()
```

    +-------------------+------+
    |               nome|vendas|
    +-------------------+------+
    |   Carminda Pestana|    23|
    |    Deolinda Vilela|    34|
    |   Emídio Dornelles|    34|
    |Felisbela Dornelles|    36|
    |   Matilde Rebouças|    22|
    +-------------------+------+



# Renomeando coluna


```python
df = despachantes.withColumnRenamed('nome', 'nomes')
df.columns
```




    ['id', 'nomes', 'status', 'cidade', 'vendas', 'data']



# Trocando tipo de dado de uma colina

Trocando valor de string para datetime


```python
from pyspark.sql.functions import to_timestamp


despachantes_type = despachantes.withColumn("data_type", to_timestamp(functions.col("data"), "yyyy-MM-dd"))
despachantes_type.select("data", "data_type").show()
```

    +----------+-------------------+
    |      data|          data_type|
    +----------+-------------------+
    |2020-08-11|2020-08-11 00:00:00|
    |2020-03-05|2020-03-05 00:00:00|
    |2020-02-05|2020-02-05 00:00:00|
    |2020-02-05|2020-02-05 00:00:00|
    |2020-02-05|2020-02-05 00:00:00|
    |2019-01-05|2019-01-05 00:00:00|
    |2019-10-05|2019-10-05 00:00:00|
    |2020-03-05|2020-03-05 00:00:00|
    |2018-05-05|2018-05-05 00:00:00|
    |2020-09-05|2020-09-05 00:00:00|
    +----------+-------------------+



Capturando apenas os anos


```python
from pyspark.sql.functions import year

despachantes_type.select(year("data")).distinct().show()
```

    +----------+
    |year(data)|
    +----------+
    |      2018|
    |      2019|
    |      2020|
    +----------+



Capturando apenas os meses


```python
from pyspark.sql.functions import month
despachantes_type.select(month("data")).distinct().show()
```

    +-----------+
    |month(data)|
    +-----------+
    |          1|
    |          3|
    |          5|
    |          9|
    |          8|
    |         10|
    |          2|
    +-----------+



# Ordenando DataFrame


```python
despachantes_type.orderBy("data").show()
```

    +---+-------------------+------+-------------+------+----------+-------------------+
    | id|               nome|status|       cidade|vendas|      data|          data_type|
    +---+-------------------+------+-------------+------+----------+-------------------+
    |  9|      Uriel Queiroz| Ativo| Porto Alegre|    54|2018-05-05|2018-05-05 00:00:00|
    |  6|   Matilde Rebouças| Ativo| Porto Alegre|    22|2019-01-05|2019-01-05 00:00:00|
    |  7|    Noêmia   Orriça| Ativo|  Santa Maria|    45|2019-10-05|2019-10-05 00:00:00|
    |  3|   Emídio Dornelles| Ativo| Porto Alegre|    34|2020-02-05|2020-02-05 00:00:00|
    |  5|     Graça Ornellas| Ativo| Porto Alegre|    12|2020-02-05|2020-02-05 00:00:00|
    |  4|Felisbela Dornelles| Ativo| Porto Alegre|    36|2020-02-05|2020-02-05 00:00:00|
    |  8|      Roque Vásquez| Ativo| Porto Alegre|    65|2020-03-05|2020-03-05 00:00:00|
    |  2|    Deolinda Vilela| Ativo|Novo Hamburgo|    34|2020-03-05|2020-03-05 00:00:00|
    |  1|   Carminda Pestana| Ativo|  Santa Maria|    23|2020-08-11|2020-08-11 00:00:00|
    | 10|   Viviana Sequeira| Ativo| Porto Alegre|     0|2020-09-05|2020-09-05 00:00:00|
    +---+-------------------+------+-------------+------+----------+-------------------+



# Agrupando DataFrame

Obter a quantidade de registros por cada ano


```python
despachantes_type.select("data").groupBy(year("data")).count().show()
```

    +----------+-----+
    |year(data)|count|
    +----------+-----+
    |      2018|    1|
    |      2019|    2|
    |      2020|    7|
    +----------+-----+



# Somando total de vendas

Total de vendas


```python
despachantes_type.select(functions.sum("vendas")).show()
```

    +-----------+
    |sum(vendas)|
    +-----------+
    |        325|
    +-----------+



Total de vendas por ano


```python
df = despachantes_type.select(year("data_type"), "vendas").withColumnRenamed("year(data_type)", "year")
df.groupBy("year").sum("vendas").show()
```

    +----+-----------+
    |year|sum(vendas)|
    +----+-----------+
    |2018|         54|
    |2019|         67|
    |2020|        204|
    +----+-----------+


#course #udemy #pypsark #python 