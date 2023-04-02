#pypsark #linux #python #pycharm #spark #apache

## Instalação do Java

```bash
sudo apt install curl mlocate default-jdk -y
```

## Baixar o Spark

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



