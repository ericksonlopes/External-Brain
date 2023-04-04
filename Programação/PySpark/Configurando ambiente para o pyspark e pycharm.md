#pypsark #linux #python #pycharm #spark #apache #windows #hadoop

# Ambiente Linux

## Instalação do Java

```bash
sudo apt install curl mlocate default-jdk -y
```

## Download o Spark

```bash
wget https://dlcdn.apache.org/spark/spark-3.3.2/spark-3.3.2-bin-hadoop3.tgz
```

## Descompactar o Spark

```bash
tar vzf spark-3.3.2-bin-hadoop3.tgz
```

## Mover o Spark para a pasta opt

```bash
sudo mv spark-3.3.2-bin-hadoop3 /opt/spark
```

## Configurar a variável de ambiente SPARK_HOME

```bash
nano ~/.bashrc
```

## Adicionar as linhas abaixo no final do arquivo

```bash 
export SPARK_HOME=/opt/spark
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
```

`ctrl + x` para sair

## Atualizar o bashrc

```bash
source ~/.bashrc
```

## iniciar o pyspark no modo standalone

```bash
start-master.sh
```

## worker process

```
/opt/spark/sbin/start-master.sh spark://localhost:7077
```

## Iniciar o pyspark no modo cluster

```bash
start-slave.sh spark://localhost:7077
```

# Ambiente windows

## Instalar o Java JDK

Faça o download e instale o Java JDK `https://www.oracle.com/br/java/technologies/downloads/`

## Instalar o Anaconda

Faça o download e instale o Anaconda `https://www.anaconda.com/`

## Instalar o Spark

Baixe o arquivo `https://www.apache.org/dyn/closer.lua/spark/spark-3.3.2/spark-3.3.2-bin-hadoop3.tgz` e descompacte na
pasta `C:\Spark`

Baixe o arquivo `winutils.exe` e coloque na pasta `C:\Spark\spark-3.3.2-bin-hadoop3\bin`

## Configurar as variáveis de ambiente

| Name                       | Value                            |
|----------------------------|----------------------------------|
| HADOOP_HOME                | C:\Spark\spark-3.3.2-bin-hadoop3 |
| SPARK_HOME                 | C:\Spark\spark-3.3.2-bin-hadoop3 |
| PYSPARK_DRIVER_PYTHON      | jupyter                          |
| PYSPARK_DRIVER_PYTHON_OPTS | notebook                         |
| PYSPARK_PYTHON             | python                           |

Dentro do PATH adicione o caminho do spark `%SPARK_HOME%\bin`

# Configurando pycharm

Selecionando o python3 como base interpreter do projeto que esteja usando o pyspark

`Settings > Project: > Project Interpreter > Add > Existing environment > Select base interpreter > /usr/bin/python3`

Configurando o pyspark no pycharm

`edit configurations > environment variables > +` e adicione as variáveis abaixo

| Name           | Value                                     |
|----------------|-------------------------------------------|
| PYSPARK_PYTHON | /usr/bin/python                           |
| PYTHONPATH     | PYTHONPATH=$SPARK_HOME/python:$PYTHONPATH |
| SPARK_HOME     | /opt/spark                                |


