#docker #redis #python

## Configurar ambiente

Instalar Redis (wsl ou linux)

```bash  
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list  

sudo apt-get updatesudo apt-get install redis
```  

`sudo service redis-server start` - Inicia o servidor

- Caso você deseje utilizar o Redis como um container Docker, siga os passos abaixo:

Baixar imagem do Redis ():

```bash
docker pull redis
```

Criar container:

```bash
docker run --name redis -p 6379:6379 -d redis
```

## Instalar dependências

```bash
pip install redis
```

## Exemplo

```python
import redis

# Criação de instância do Redis
redis_pool = redis.ConnectionPool(host='localhost', port=6379, db=0)
redis_client = redis.Redis(connection_pool=redis_pool)

# Inserção de dados
redis_client.set('nome', 'João')

# Recuperação de dados
print(redis_client.get('nome'))
```




