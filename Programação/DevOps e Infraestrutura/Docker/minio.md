#docker #dockerfile #minio #bucket #docker-compose 
```dockerfile

FROM minio/minio:latest  
  
VOLUME ["/data"]  
  
EXPOSE 9000 9001  
  
ENV MINIO_ROOT_USER=admin  
ENV MINIO_ROOT_PASSWORD=admin123  
  
ENTRYPOINT ["minio"]  
CMD ["server", "/data", "--console-address", ":9001"]
```

```yaml
version: '3.8'

services:
  minio:
    image: minio/minio:latest
    container_name: minio
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      MINIO_ROOT_USER:
      MINIO_ROOT_PASSWORD:

    volumes:
      - /var/docker_volumes/minio/data:/data
    command: server /data --console-address ":9001"
    networks:
      - connect

networks:
  connect:
    external: true
```