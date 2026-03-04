# Criação de tabelas


```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("Joins").getOrCreate()

professores = [("João", 1), ("Maria", 2), ("Pedro", 3), ("Ana", 4)]
disciplinas = [("Matemática", 1), ("Português", 2), ("Inglês", 3), ("Espanhol", 4)]

professoresDF = spark.createDataFrame(data=professores, schema=["nome", "id"])
disciplinasDF = spark.createDataFrame(data=disciplinas, schema=["nome", "id"])

professoresDF.write.saveAsTable("professores")
disciplinasDF.write.saveAsTable("disciplinas")
```

                                                                                    

# Exibindo tabelas


```python
spark.sql("show tables").show()
```

    +---------+-----------+-----------+
    |namespace|  tableName|isTemporary|
    +---------+-----------+-----------+
    |  default|disciplinas|      false|
    |  default|professores|      false|
    +---------+-----------+-----------+
    
    

# Join com SQL


```python
spark.sql("""
SELECT p.nome, d.nome
FROM professores AS p
LEFT JOIN disciplinas AS d ON p.id = d.id"""
          ).show()
```

    +-----+----------+
    | nome|      nome|
    +-----+----------+
    | João|Matemática|
    |Pedro|    Inglês|
    |Maria| Português|
    |  Ana|  Espanhol|
    +-----+----------+
    
    

# Join com DataFrame


```python
professoresDF.join(disciplinasDF, "id", "left").show()
```

    +---+-----+----------+
    | id| nome|      nome|
    +---+-----+----------+
    |  1| João|Matemática|
    |  2|Maria| Português|
    |  3|Pedro|    Inglês|
    |  4|  Ana|  Espanhol|
    +---+-----+----------+
    
    

`outer` - Combina todos os registros de ambos os DataFrames, mesmo que não haja correspondência entre eles.
`left` - Combina todos os registros do DataFrame da esquerda, mesmo que não haja correspondência entre eles.
`right` - Combina todos os registros do DataFrame da direita, mesmo que não haja correspondência entre eles.


#python #pyspark #joins #sql #dataframe
