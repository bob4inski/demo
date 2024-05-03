# How to install docker 


Либо переходите по ссылке https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository
Либо делайте команды снизу 

P.S Спойлер они одинаковые
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin


# После установки

1. отредактировать /etc/docker/daemon.json : sudo nano /etc/docker/daemon.json


записать туда
{
  "live-restore": true,
  "bip": "172.20.0.1/16",
  "default-address-pools": [{
    "base": "172.20.0.0/8",
    "size": 16
  }]
}

важная строка "bip": "172.20.0.1/16" - это смена подсети докера, чтобы она не конфликтовала с адресом сервера авторизации wi-fi

после сохранения файла перезапустить службу докера: sudo service docker restart
и проверить настройки sudo docker network inspect bridge

Там должен появиться диапазон 172.20.0.0/16

