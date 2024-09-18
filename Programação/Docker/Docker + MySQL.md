#docker #mysql #sql #python #sqlalchemy #orm 

```dockerfile
# Use an official MySQL runtime as a parent image  
FROM mysql:latest  
  
# Set the MySQL root password  
ENV MYSQL_ROOT_PASSWORD=root_password  
  
# Create a database and user  
ENV MYSQL_DATABASE=spaiglass_core  
ENV MYSQL_USER=hootzpa  
ENV MYSQL_PASSWORD=test_password  
  
# Expose the MySQL port  
EXPOSE 3306
```

Conectando com python e mysql utilizando sqlalchemy

```python
from sqlalchemy import create_engine, Column, Integer, String, Sequence  
from sqlalchemy.ext.declarative import declarative_base  
from sqlalchemy.orm import sessionmaker, Session  
  
# Configuração do SQLAlchemy para MySQL  
DATABASE_URL = "mysql+pymysql://hootzpa:test_password@localhost:3306/spaiglass_core"  
engine = create_engine(DATABASE_URL, echo=True)  
  
# Definição da base de modelo usando SQLAlchemy  
Base = declarative_base()  
  
  
class User(Base):  
    __tablename__ = 'users'  
  
    id = Column(Integer, Sequence('user_id_seq'), primary_key=True)  
    name = Column(String(50))  
    age = Column(Integer)  
  
  
# Criação das tabelas no banco de dados  
# Base.metadata.create_all(bind=engine)  
  
session = Session(engine)  
  
# Adicionar um usuário  
new_user = User(name='John 3', age=25)  
new_user1 = User(name='John 2', age=25)  
session.add(new_user)  
session.add(new_user1)  
session.commit()  
  
# Consultar usuários  
users = session.query(User).all()  
for user in users:  
    print(f"ID: {user.id}, Name: {user.name}, Age: {user.age}")  
  
# Fechar a sessão  
session.close()
```
