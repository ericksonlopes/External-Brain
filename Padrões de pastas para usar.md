app/
|__ main.py
|__ config.py
|__ requirements.txt
|__ app/
    |__ interfaces/
        |__ routes.py
        |__ views.py
    |__ application/
        |__ services.py
        |__ repositories.py
    |__ infrastructure/
        |__ database.py
        |__ models.py

-   `main.py`: Este é o arquivo principal que inicia o aplicativo Flask e carrega as configurações do `config.py`.
    
-   `config.py`: Aqui você pode definir as configurações gerais do aplicativo, como o URL do banco de dados e outras configurações de Flask.
    
-   `requirements.txt`: Este arquivo especifica as dependências externas do projeto.
    
-   `interfaces/routes.py`: Aqui você pode implementar as rotas do Flask, que lidam com as solicitações HTTP recebidas pelo aplicativo.
    
-   `interfaces/views.py`: Aqui você pode implementar as views do Flask, que renderizam os templates HTML do aplicativo.
    
-   `application/services.py`: Aqui você pode implementar os serviços de aplicação, que lidam com a lógica de negócios do aplicativo, como a validação de entrada de dados.
    
-   `application/repositories.py`: Aqui você pode implementar os repositórios de aplicação, que lidam com a persistência dos dados no banco de dados.
    
-   `infrastructure/database.py`: Aqui você pode implementar a conexão com o banco de dados, usando SQLAlchemy ou outro ORM.
    
-   `infrastructure/models.py`: Aqui você pode definir os modelos do SQLAlchemy, que representam as tabelas do banco de dados.


app/
|__ main.py
|__ config.py
|__ requirements.txt
|__ app/
    |__ interfaces/
        |__ __init__.py
        |__ routes.py
        |__ views.py
    |__ application/
        |__ __init__.py
        |__ services/
            |__ __init__.py
            |__ task_service.py
        |__ repositories/
            |__ __init__.py
            |__ task_repository.py
    |__ infrastructure/
        |__ __init__.py
        |__ database.py
        |__ models.py

-   `main.py`: Este é o arquivo principal que inicia o aplicativo Flask e carrega as configurações do `config.py`.
    
-   `config.py`: Aqui você pode definir as configurações gerais do aplicativo, como o URL do banco de dados e outras configurações de Flask.
    
-   `requirements.txt`: Este arquivo especifica as dependências externas do projeto.
    
-   `interfaces/routes.py`: Aqui você pode implementar as rotas do Flask, que lidam com as solicitações HTTP recebidas pelo aplicativo.
    
-   `interfaces/views.py`: Aqui você pode implementar as views do Flask, que renderizam os templates HTML do aplicativo.
    
-   `application/services/task_service.py`: Aqui você pode implementar os serviços de aplicação relacionados a tarefas, que lidam com a lógica de negócios do aplicativo, como a validação de entrada de dados e manipulação de tarefas.
    
-   `application/repositories/task_repository.py`: Aqui você pode implementar os repositórios de aplicação relacionados a tarefas, que lidam com a persistência dos dados no banco de dados.
    
-   `infrastructure/database.py`: Aqui você pode implementar a conexão com o banco de dados, usando SQLAlchemy ou outro ORM.
    
-   `infrastructure/models.py`: Aqui você pode definir os modelos do SQLAlchemy, que representam as tabelas do banco de dados.