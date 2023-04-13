#python #pyspark 

```python  
from pyspark.sql import SparkSession  
  
spark = SparkSession.builder.appName('Tabelas').getOrCreate()  
  
df = spark.createDataFrame([('a', 1), ('b', 2), ('c', 3)], ['col1', 'col2'])  
```  
  
# Criando uma tabela temporária  
  
  
```python  
df.createOrReplaceTempView('tabela_temp')  
```  
  
# Criando uma tabela permanente  
  
  
```python  
df.createOrReplaceTempView('tabela_permanente')  
df.write.mode('overwrite').saveAsTable('tabela_permanente')  
```  
  
# Criando uma tabela global temporária  
  
  
```python  
df.createGlobalTempView('tabela_global_temp')  
```  
  
# Exibindo as tabelas  
  
Para acessar as tabelas globais basta adicionar o prefixo global_temp.  
  
  
```python  
spark.sql("show tables").show()  
spark.sql("select * from global_temp.tabela_global_temp").show()  
```  
  
+---------+-----------------+-----------+  
|namespace| tableName|isTemporary|  
+---------+-----------------+-----------+  
| default|tabela_permanente| false|  
| |tabela_permanente| true|  
| | tabela_temp| true|  
+---------+-----------------+-----------+  
  
+----+----+  
|col1|col2|  
+----+----+  
| a| 1|  
| b| 2|  
| c| 3|  
+----+----+  
  
  
  
  
  
  
```python  
spark.sql("drop table if exists tabela_temp")  
spark.sql("drop table if exists tabela_permanente")  
spark.sql("drop table if exists global_temp.tabela_global_temp")  
```  
  
  
  
  
DataFrame[]