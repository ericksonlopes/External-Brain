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



