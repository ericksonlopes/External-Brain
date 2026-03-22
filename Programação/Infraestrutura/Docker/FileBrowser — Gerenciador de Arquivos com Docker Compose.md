# 🐳 FileBrowser — Gerenciador de Arquivos com Docker Compose

Interface web rápida e leve para gerenciar arquivos dentro de volumes do Docker.

## 📦 Dependências

- Docker & Docker Compose

## ⚙️ Implementação

`yaml
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
`

## 📌 Notas

- Mapeia /var/docker_volumes do host para o root /srv do browser, permitindo gerenciar dados de outros containers.

---
#docker #infra #devops #file-management
