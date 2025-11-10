
version: "3.7"  
  
services:  
  vernemq:  
    image: vernemq/vernemq:latest  
    container_name: vernemq  
    restart: unless-stopped  
    ports:  
      - "1883:1883" # MQTT TCP (porta 1884 no host -> 1883 no container)  
      - "8080:8080" # Web Admin UI (opcional)  
      - "8083:8083" # WebSocket sem TLS (para frontend)  
    environment:  
      - DOCKER_VERNEMQ_ALLOW_ANONYMOUS=on  
      - DOCKER_VERNEMQ_LISTENER__TCP__DEFAULT=0.0.0.0:1883  
      - DOCKER_VERNEMQ_LISTENER__WS__DEFAULT=0.0.0.0:8083  
      - DOCKER_VERNEMQ_LISTENER__WS__MOUNTPOINT=/  
      - DOCKER_VERNEMQ_ACCEPT_EULA=yes