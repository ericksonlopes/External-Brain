#docker #dockerfile #minio
```dockerfile

FROM minio/minio:latest  
  
VOLUME ["/data"]  
  
EXPOSE 9000 9001  
  
ENV MINIO_ROOT_USER=admin  
ENV MINIO_ROOT_PASSWORD=admin123  
  
ENTRYPOINT ["minio"]  
CMD ["server", "/data", "--console-address", ":9001"]
```
