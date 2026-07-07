

sudo apt update && sudo apt upgrade -y

curl -sSL https://get.docker.com | sh

sudo usermod -aG docker name_user

docker volume create portainer_data

	docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

pegar o ip
hostname -I 

  pegar o Setup Token
docker logs portainer 2>&1 | grep -i "token"

docker restart portainer

docker run -d --name=dashdot \ -p 3333:3001 \ -v /:/mnt/host:ro \ --privileged \ --restart always \ mauricenino/dashdot:latest