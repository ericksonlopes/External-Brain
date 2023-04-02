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
echo "export SPARK_HOME=/opt/spark" >> ~/.bashrc

echo "export PATH=$PATH:$SPARK_HOME/sbin" >> ~/.bashrc
```