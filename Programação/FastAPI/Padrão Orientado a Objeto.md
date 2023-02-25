#fastAPI #

`/models/car.py
```python
from pydantic import BaseModel  
  
  
class Car(BaseModel):  
    id: str  
    name: str
```

`/controller/car_controller.py
```python
from models.Car import Car  
  
  
class CarController:  
    @classmethod  
    async def get(cls) -> Car:  
        return Car(id="1", name="BMW")
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