#docker #mysql #sql 

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