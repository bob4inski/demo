# ЧТобы запустить MediaWiki

нужно чтобы был установлен докер

1. Создаем файл docker-compose-media-wiki.yml

```bash
touch ~/wiki.yml
nano ~/wiki.yml
# туда скопировать содержимое файли из этой папки с названием docker-compose.yml

```

2. Поднимаем вики
```bash
sudo docker volume create dbvolume
sudo docker compose -f wiki.yml up -d
```

3. Потом идет онлайн установка Wiki
Переходим по адресу 
http://<ip адрес пк с которого запускаете>:8081

4. На моменте установки надо будет указать логин пароль и адрес подключения к базе
Для подключения к базе надо будет указать имя контейнера с бд - db

5. После конца установки надо будет скачать файлик LocalSetting.php

6. Как только скачали файл то меняем в docker-compose файле строку с      # - ./LocalSettings.php:/var/www/html/LocalSettings.php
ЕЕ надо расскоментировать!

7. Создаете файл LocalSettings.php 
```bash
touch ~/LocalSettings.php
nano ~/LocalSettings.php 
#вставляете все содержимое в файл или копируете скаченный файл в папку пользователя. где лежит wiki.yml

#перезапускаете docker
sudo docker compose -f wiki.yml up --build -d
```

