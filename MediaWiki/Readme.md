# Запуск MediaWiki
## Установка Docker
```
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

# Add the repository to Apt sources: (не пугайтесь это огромная команда на 4 строки)
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
sudo apt-get update

` sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin `

## MediaWiki
0. Выполните команду cat docker-compose.yml и скопируйте содержимое файла

1. Создайте файл docker-compose-media-wiki.yml с помощью команд:

cd /demo/MediaWiki/
touch ~/wiki.yml
nano ~/wiki.yml # Вставьте в файл ~/wiki.yml содержимое файла docker-compose.yml из п.0


3. Поднимаем MediaWiki:
   
sudo docker volume create dbvolume
sudo docker compose -f wiki.yml up -d

3. Вспомните адрес устройства с помощью команды ip a (он будет указан у интерфейса ens)

4. Онлайн-установка Wiki:
- Перейдите по адресу http://<ip адрес, который узнали в п.3>:8080

4. Настал момент установки. Укажите логин (мы делали mediawiki),пароль (указаны в докер-композ файле) и хост базы данных - db (да, всего две буквы)
5. На странице установки введите кастомное название (желательно с вашим именем), выбрать "то же, что иия вики" в пункте пространство имен
6. Задайте имя и пароли, электронную почту и выберете пункт "Хватит, установить вики"
7. После конца установки нажмите кнопку "Скачать" для загрузки файла LocalSetting.php
8. Перейдите в файл wiki.yml с помощью команды nano ~/wiki.yml
9. В этом файле РАССКОМЕНТИРУЙТЕ # - ./LocalSettings.php:/var/www/html/LocalSettings.php
10. Из загрузок откройте скаченный файл LocalSetting.php в текстовом редакторе и скопируйте его содержимое
11. Выполните команду  touch ~/LocalSettings.php и перейдите в файл с помощью команды nano ~/LocalSettings.php и вставьте содержимое с п.8
12. Перезапустите docker с помощью команды sudo docker compose -f wiki.yml up --build -d
