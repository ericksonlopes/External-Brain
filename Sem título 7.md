version: "3.8"
services:
  filebrowser:
    image: filebrowser/filebrowser
    container_name: file_browser
    ports:
      - "8151:80"
    volumes:
      - /var/docker_volumes:/srv
      - filebrowser_config:/config
    restart: unless-stopped

volumes:
  filebrowser_config:
    driver: local
