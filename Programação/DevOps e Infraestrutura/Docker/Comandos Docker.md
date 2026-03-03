# 🐳 Guia de Comandos Docker e Compose

Este documento serve como uma folha de cola (Cheat Sheet) rápida para as operações mais comuns do Docker no dia a dia.

---

## 🏗️ Docker CLI (Imagens e Containers)

### Gerenciar Imagens
- **Build**: Cria uma imagem a partir de um Dockerfile.
  ```bash
  docker build -t nome-da-imagem .
  ```
- **Listar**: `docker images`
- **Remover**: `docker rmi id-da-imagem`

### Gerenciar Containers
- **Executar (Run)**: Inicia um novo container.
  - `-d`: Roda em background (detached).
  - `-it`: Modo interativo com terminal.
  - `--name`: Dá um nome amigável ao container.
  ```bash
  docker run --name meu-app -d -it nome-da-imagem
  ```
- **Mapeamento de Portas**: Libera o acesso externo ao container.
  ```bash
  docker run -p 3000:3000 nome-da-imagem
  ```
- **Parar/Iniciar**: `docker stop nome` / `docker start nome`
- **Remover**: `docker rm -f nome` (Força a remoção mesmo se estiver rodando).

---

## 🐙 Docker Compose (Orquestração Local)

O Compose é ideal para rodar múltiplos serviços (ex: App + Banco de Dados) definidos em um arquivo `yaml`.

- **Subir tudo**: 
  ```bash
  docker-compose up -d
  ```
- **Parar e Remover**: 
  ```bash
  docker-compose down
  ```
- **Ver Status**: 
  ```bash
  docker-compose ps
  ```
- **Logs em Tempo Real**: 
  ```bash
  docker-compose logs -f
  ```
- **Entrar no Container**:
  ```bash
  docker-compose exec nome-do-servico bash
  ```

---
#docker #devops #cheatsheet #infra #linux
