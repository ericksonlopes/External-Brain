#fastAPI #MVC #design-patterns #python

O padrão MVC é usado em muitos tipos de aplicativos, como aplicativos web, desktop e móveis. Ele ajuda a separar a lógica de negócios da interface do usuário, tornando o código mais organizado e fácil de entender e manter. O padrão MVC também ajuda a promover a reutilização de código, pois as diferentes camadas podem ser desenvolvidas separadamente.

A arquitetura de sistema com FastAPI pode conduzir um padrão de arquitetura MVC (Model-View-Controller) ou similar, onde a lógica de negócios (Model) é separada da interface do usuário (View) e controlada por um controlador (Controller).

1.  Models: Representa os dados e as regras de negócios relacionadas a eles

Responsável por lidar com a lógica de negócios do aplicativo e acessar os dados. Ela pode ser implementada usando uma biblioteca de ORM, como SQLAlchemy ou Tortoise ORM. Com o ORM, você pode definir os seus modelos de banco de dados como classes Python e, em seguida, interagir com o banco de dados usando essas classes.

`/models/car.py
```python
from pydantic import BaseModel  
  
  
class Car(BaseModel):  
    id: str  
    name: str
```


2.  Controller: Lida com a lógica de controle e gerência as interações entre o modelo e a visualização

A camada do controlador é responsável por controlar o fluxo de dados entre o modelo e a visualização. Ela pode ser implementada como uma camada intermediária que recebe solicitações da visualização, chama a camada do modelo para obter dados e, em seguida, retorna uma resposta adequada à visualização.

`/controller/car_controller.py
```python
from models.Car import Car  
  
  
class CarController:  
    @classmethod  
    async def get(cls) -> Car:  
        return Car(id="1", name="BMW")
```


3.  Views: Responsável pela apresentação dos dados ao usuário

É responsável por receber solicitações do cliente e retornar uma resposta adequada. Ela pode ser implementada usando rotas FastAPI que correspondem às URLs da API. A view pode chamar a camada do modelo para obter dados e, em seguida, usar um esquema FastAPI para serializar a resposta em um formato adequado, como JSON.

`/views/car_view.py
```python
from fastapi import APIRouter  
  
from controllers.car_controller import CarController  
from models.Car import Car  
  
car = CarController()  
  
router_car = APIRouter()  
  
  
@router_car.get("/car", response_model=Car, summary="Get car")  
async def get_todos() -> Car:  
    return await car.get()
```

`main.py

```python
from fastapi import FastAPI  
  
from views.car_view import router_car  
  
app = FastAPI()  
  
app.router.include_router(router_car, prefix="/api/v1", tags=["car"])  
  
if __name__ == "__main__":  
    import uvicorn  
  
    uvicorn.run(app, host="localhost", port=8000)

```

Em resumo, o padrão MVC é uma maneira eficaz de organizar o código em sua aplicação FastAPI, separando a lógica de negócios (Model), as rotas e funções de manipulação de solicitações HTTP (Controller) e a exibição dos dados (View).