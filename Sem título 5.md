version: '3.8'

services:
  minio:
    image: minio/minio:latest
    container_name: minio
    restart: unless-stopped
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      MINIO_ROOT_USER: ADMINMINIOROOT
      MINIO_ROOT_PASSWORD: ZQJoESbQOfA5#6*mUsA

    volumes:
      - /var/docker_volumes/minio/data:/data
    command: server /data --console-address ":9001"
    networks:
      - connect

networks:
  connect:
    external: true