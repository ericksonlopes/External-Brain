# 📊 Exportando Dados com PySpark e Pandas

Este guia mostra como ler dados de um arquivo CSV, definir um schema tipado e exportar o DataFrame resultante para diversos formatos (Parquet, JSON, ORC, Excel).

## 🛠️ Lendo CSV com Schema Definido

Definir o schema manualmente evita que o Spark precise ler o arquivo duas vezes para inferir os tipos, melhorando a performance.

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("ExportacaoDados").getOrCreate()

# Definindo a estrutura
arqschema = "id int, nome string, status string, cidade string, vendas int, data string"

# Lendo o arquivo
df = spark.read.csv("arquivos/despachantes.csv", header=False, schema=arqschema)
df.show(5)
```

## 📤 Exportando via PySpark (Nativo)

O Spark exporta por padrão em pastas particionadas, o que é ideal para Big Data.

```python
# Formatos comuns
df.write.csv("output/csv_export")
df.write.parquet("output/parquet_export")
df.write.json("output/json_export")
df.write.orc("output/orc_export")
```

## 🐼 Exportando via Pandas (Para Arquivos Únicos)

Se o volume de dados couber na memória e você precisar de um arquivo único (como um `.xlsx`), converta para Pandas:

```python
import pandas as pd

# Converte Spark DF para Pandas DF
df_pandas = df.toPandas()

# Exportações
df_pandas.to_csv("output/final.csv", index=False)
df_pandas.to_excel("output/final.xlsx", index=False)
df_pandas.to_parquet("output/final.parquet")
```

---
#pyspark #pandas #etl #data-engineering #python #csv #parquet
