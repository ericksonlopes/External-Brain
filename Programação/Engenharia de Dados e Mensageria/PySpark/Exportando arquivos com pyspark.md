```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("dados").getOrCreate()

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
    
    

# Salvar o dataframe em um arquivo csv, parquet, json e orc com PySpark


```python
despachantes.write.csv("output/despachantes.csv")
despachantes.write.format("parquet").save("output/despachantesparquet")
despachantes.write.format("json").save("output/despachantesjson")
despachantes.write.format("orc").save("output/despachantesorc")

```

# Salvar o dataframe em um arquivo csv, parquet, json e orc com Pandas


```python
import pandas as pd
# import openpyxl
# import pyarrow

df = pd.DataFrame(despachantes.collect(), columns=despachantes.columns)

df.to_csv("output/despachantes.csv", index=True)
df.to_parquet("output/despachantes.parquet")
df.to_json("output/despachantes.json")
df.to_excel("output/despachantes.xlsx")
```

#python #pyspark 