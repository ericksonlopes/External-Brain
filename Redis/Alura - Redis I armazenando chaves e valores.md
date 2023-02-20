#redis #alura  
  
# Instalação  
  
````bash  
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg  
echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list  
sudo apt-get updatesudo apt-get install redis````  
  
`sudo service redis-server start` - Inicia o servidor  
  
`sudo service redis-server stop` - Para o servidor  
  
`redis-cli` - Inicia o cliente  
  
`ping` - Verifica se o servidor está ativo  
  
# Comandos  
  
`set nome "Alura"` - Cria uma chave com o valor  
  
`get nome` - Recupera o valor da chave  
  
`del nome` - Remove a chave  
  
`keys *` - Lista todas as chaves  
  
## keys  
  
`resultado:18-05-2021:megasena "12 ,3, 4, 5, 6," ` - Exemplo de chave  
  
`keys "resultado:*"` - Lista todas as chaves que começam com resultado  
  
`keys "resultado:1?-05-2021:megasena"` - Lista todas as chaves que começam com resultado e terminam com megasena  
  
`keys "resultado:1[37]-05-2021:megasena"` - Lista todas as chaves que começam com resultado e terminam com megasena e o  
segundo caractere é 3 ou 7  
  
`*` - Qualquer coisa  
`?` - Qualquer caractere  
`[]` - Qualquer caractere dentro do conjunto  
`-` - Qualquer caractere dentro do intervalo  
`!` - Qualquer caractere que não esteja no conjunto  
  
# hash  
  
`hset resultado:18-05-2021:megasena "numeros" "12 ,3, 4, 5, 6,"` - Cria uma chave com o valor  
`hget resultado:18-05-2021:megasena "numeros"` - Recupera o valor da chave  
`hdel resultado:18-05-2021:megasena "numeros"` - Remove a chave  
  
`hmset resultado:18-05-2021:megasena "numeros" "12 ,3, 4, 5, 6," "data" "18-05-2021"` - Cria uma chave com o valor  
`hmget resultado:18-05-2021:megasena "numeros" "data"` - Recupera o valor da chave  
`hgetall resultado:18-05-2021:megasena` - Recupera todos os valores da chave  
  
# Expire  
  
`set nome "Alura"` - Cria uma chave com o valor  
`expire nome 100` - Define o tempo de expiração da chave em segundos (pode mudar enquanto ele esa sendo executado  
  
# TTL  
  
`ttl nome` - Tempo restante para expirar a chave  
  
# INCR e DECR (incremento e decremento)  
  
`set visitas 0` - Cria uma chave com o valor  
`incr visitas` - Incrementa o valor da chave  
`decr visitas` - Decrementa o valor da chave  
  
# INCRBY e DECRBY (incremento e decremento)  
  
`set visitas 0` - Cria uma chave com o valor  
`incrby visitas 10` - Incrementa o valor da chave em 10  
`decrby visitas 10` - Decrementa o valor da chave em 10  
  
# Incr e Decr com hash  
  
`hset resultado:18-05-2021:megasena "numeros" "12 ,3, 4, 5, 6,"` - Cria uma chave com o valor  
`hincrby resultado:18-05-2021:megasena "numeros" 10` - Incrementa o valor da chave em 10  
`hdecrby resultado:18-05-2021:megasena "numeros" 10` - Decrementa o valor da chave em 10  
  
# incrbyfloat e decrbyfloat  
  
`set visitas 0` - Cria uma chave com o valor  
`incrbyfloat visitas 10.5` - Incrementa o valor da chave em 10.5  
`decrbyfloat visitas 10.5` - Decrementa o valor da chave em 10.5  
  
# GETBIT e SETBIT (bit a bit)  
  
`setbit visitas 0 1` - Define o bit 0 como 1  
`getbit visitas 0` - Recupera o valor do bit 0  
  
`setbit acesso:25-05-2023 0 1` - Define o bit 0 como 1  
`getbit acesso:25-05-2023 0` - Recupera o valor do bit 0  
  
# BITCOUNT  
  
`bitcount acesso:25-05-2023` - Conta quantos bits estão ativos  
  
# BITOP (operadores bit a bit)  
  
`setbit acesso:25-05-2023 0 1` - Define o bit 0 como 1  
  
`setbit acesso:26-05-2023 1 1` - Define o bit 1 como 1  
  
`bitop and resultado:25-05-2023 acesso:25-05-2023 acesso:25-05-2023` - Realiza a operação and entre os dois bits  
`bitop or resultado:25-05-2023 acesso:25-05-2023 acesso:25-05-2023` - Realiza a operação or entre os dois bits