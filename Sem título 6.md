version: "3.8"
services:
  database:
    image: postgres:17
    container_name: postgres
    restart: unless-stopped
    environment:
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=Af6fP1Gdliu8C5AL!9AD
      - POSTGRES_DB=postgres
      - TZ=America/Sao_Paulo
      - PGTZ=America/Sao_Paulo
    ports:
      - 5432:5432
    volumes:
      - /var/docker_volumes/postgres/data:/var/lib/postgresql/data
      - /var/docker_volumes/postgres/postgresql.conf:/etc/postgresql/postgresql.conf

    entrypoint: 
      docker-entrypoint.sh -c 'config_file=/etc/postgresql/postgresql.conf'
    networks:
      - connect

networks:
  connect:
    external: true
