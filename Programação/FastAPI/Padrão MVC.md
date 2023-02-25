#fastAPI #MVC #design-patterns 

A arquitetura de sistema com FastAPI pode seguir um padrão de arquitetura MVC (Model-View-Controller) ou similar, onde a lógica de negócios (Model) é separada da interface do usuário (View) e controlada por um controlador (Controller).

1.  Pasta "models": contém os modelos de dados do sistema.
`/models/car.py
```python
from pydantic import BaseModel  
  
  
class Car(BaseModel):  
    id: str  
    name: str
```

2.  Pasta "controllers": contém os controladores do sistema.
`/controller/car_controller.py
```python
from models.Car import Car  
  
  
class CarController:  
    @classmethod  
    async def get(cls) -> Car:  
        return Car(id="1", name="BMW")
```

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

`/controller/car_controller.py

```python
from fastapi import FastAPI  
  
from views.car_view import router_car  
  
app = FastAPI()  
  
app.router.include_router(router_car, prefix="/api/v1", tags=["car"])  
  
if __name__ == "__main__":  
    import uvicorn  
  
    uvicorn.run(app, host="localhost", port=8000)

```