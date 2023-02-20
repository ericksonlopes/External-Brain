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
`hset resultado:18-05-2021:megasena "valores" "12 ,3, 4, 5, 6,"` - Cria uma chave com o valor  
  
`hget resultado:18-05-2021:megasena "numeros"` - Recupera o valor da chave  
  
`hdel resultado:18-05-2021:megasena "numeros"` - Remove a chave