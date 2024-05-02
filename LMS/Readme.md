# Как поднять LMS

1. Скачиваем докер композ файл с офиц репозитория 
```bash
curl -sSL https://raw.githubusercontent.com/bitnami/containers/main/bitnami/moodle/docker-compose.yml > docker-compose.yml
sudo docker compose up -d
```

2. После того, как контейнеры поднялись идем по ссылке

http://<ip компьютера на котором развернули>:80

И нужно авторизоваться
Пользователь - user
Пароль - bitnami 

после авторизации необходимо перейти во вкладку "site administration", где необходимо заполнить поля с красным восклицательным знаком

нажать "register your site", после continue 

пролистать вниз до заголовка "Site home", выбрать  "Site home settings" и укажите новое название сайта, короткое имя и сохраните изменения (если все успешно, сверху появится ненавязчивая зеленая плашка Changes saved)

Снова перейдите в "Site administration" -> "Users" -> Accounts (заголовок) -> Upload users

Нажмите choose a file, upload a file -> обзор

Найдите файл users.csv (Домашняя папка -> demo -> LMS -> users.csv) и выберете его

Нажмите Upload this file

CSV separator выберете ;

В Settings:
upload type: add new only, skip existing users
new user password: field requires in file
force password: none

Сохраните 

Снова перейдите на вкладку Users и выберете Cohorts (у заголовка Accounts) -> Add new cohorts и создайте команды согласно таблице (Team,Admin, Manager, WS)

После созданных груп, нажмите на шестеренку возле каждый, выберете Assign и добавьте пользователей согласно таблице
