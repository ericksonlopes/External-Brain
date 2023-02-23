#docker #redis #python

## Configurar ambiente



Baixar imagem do Redis (Caso você deseje utilizar o Redis como um container):

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
r = redis.Redis(host='localhost', port=6379, db=0)

# Inserção de dados
r.set('nome', 'João')

# Recuperação de dados
print(r.get('nome'))
```



