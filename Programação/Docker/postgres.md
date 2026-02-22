```d
services:  
  postgres:  
    image: postgres:15-alpine  
    container_name: alembic_postgres  
    environment:  
      POSTGRES_DB: alembic_tutorial  
      POSTGRES_USER: tutorial_user  
      POSTGRES_PASSWORD: tutorial_password  
    ports:  
      - "5432:5432"  
    volumes:  
      - postgres_data:/var/lib/postgresql/data  
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql  
    healthcheck:  
      test: ["CMD-SHELL", "pg_isready -U tutorial_user -d alembic_tutorial"]  
      interval: 10s  
      timeout: 5s  
      retries: 5  
    networks:  
      - alembic_network
```