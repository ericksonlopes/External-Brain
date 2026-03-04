#docker #docker #volumes #logging

Claro! Aqui está um exemplo de como salvar logs em um arquivo e usar um volume no Docker para persistir os dados:

```python
import logging

# Configuração do logger
logging.basicConfig(filename='/logs/app.log', level=logging.DEBUG,
format='%(asctime)s %(levelname)s %(name)s %(message)s')

# Exemplo de mensagem de log
logging.info('Mensagem de informação')
```

Para persistir os logs em um volume no Docker, você pode criar um volume e montá-lo no diretório de logs do contêiner. Por exemplo:

```bash
docker volume create logs_volume
docker run -d --name my_app -v logs_volume:/logs my_app_image
```

Isso criará um contêiner a partir da imagem my_app_image e montará o volume logs_volume no diretório /logs do contêiner. Os logs gerados pelo aplicativo serão salvos nesse diretório e serão persistidos mesmo após o contêiner ser excluído.