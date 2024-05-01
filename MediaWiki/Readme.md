# Запуск MediaWiki
## Установка Docker
```
apt-get update
apt-get install -y  ca-certificates curl
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
chmod a+r /etc/apt/keyrings/docker.asc
```
## MediaWiki

1. Создайте файл docker-compose-media-wiki.yml с помощью команд:

```bash
cd /demo/MediaWiki/
cat docker-compose.yml # Скопируйте содержимое файла docker-compose.yml 
touch ~/wiki.yml
nano ~/wiki.yml # Вставьте в файл ~/wiki.yml содержимое файла docker-compose.yml  
```

2. Поднимаем MediaWiki:
   
```bash
sudo docker volume create dbvolume
sudo docker compose -f wiki.yml up -d
```
3. Вспомните адрес устройства с помощью команды ip a

4. Онлайн-установка Wiki:
- Перейдите по адресу http://<ip адрес, который узнали в п.3>:8081

4. Настал момент установки. Укажите логин,пароль (указаны в задании) и адрес подключения к базе - db (да, всего две буквы)
5. После конца установки нажмите кнопку "Скачать" для загрузки файла LocalSetting.php
6. Перейдите в файл wiki.yml с помощью команды `nano ~/wiki.yml`
7. В этом файле РАССКОМЕНТИРУЙТЕ # - ./LocalSettings.php:/var/www/html/LocalSettings.php
8. Из загрузок откройте скаченный файл LocalSetting.php в текстовом редакторе и скопируйте его содержимое
9. Выполните команду ` touch ~/LocalSettings.php` и перейдите в файл с помощью команды `nano ~/LocalSettings.php` и вставьте содержимое с п.8


10. Перезапустите docker с помощью команды `sudo docker compose -f wiki.yml up --build -d`


