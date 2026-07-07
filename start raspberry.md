```bash
sudo apt update && sudo apt upgrade -y
```

```bash
curl -sSL https://get.docker.com | sh
```

```bash
sudo usermod -aG docker name_user
```

```bash
docker volume create portainer_data
```

```bash
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

pegar o ip

```bash
hostname -I 
```

pegar o Setup Token

```bash
docker logs portainer 2>&1 | grep -i "token"
```

```bash
docker restart portainer
```

```bash
docker run -d --name=dashdot \ -p 3333:3001 \ -v /:/mnt/host:ro \ --privileged \ --restart always \ mauricenino/dashdot:latest
```