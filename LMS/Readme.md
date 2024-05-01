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

Далее будет такая менюшка

![alt text](image.png)

Нужно зайти в site administration и промотать в самый низ до панели Site Home![alt text](image-1.png)

И в разделе Site home settings меняем Full Site name