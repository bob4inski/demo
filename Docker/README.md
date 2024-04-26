# How to install docker 

chmod +x ./install.sh

sudo ./install.sh


# Задание поднять LMS
curl -sSL https://raw.githubusercontent.com/bitnami/containers/main/bitnami/moodle/docker-compose.yml > docker-compose.yml

sudo docker compose up -d 

##  Если не будет интернета - то запустить docker-compose из этого репозитория
docker compose up -d


## Задание поднять в контейнере бд с использованием volume 
mkdir /home/postgres_mount #так мы создаем папку, которую будем монтировать в контейнер
 
sudo docker run -d \
	-p 5432:5432
	--name my-postgres \
	-e POSTGRES_PASSWORD=mysecretpassword \
	-e PGDATA=/var/lib/postgresql/data/pgdata \
	-v /home/postgres_mount:/var/lib/postgresql/data \
	postgres


## Проброс портов 
Достаточно добавить к строке запуска контейнера флаг -p
Сначала идет порт компуктера, потом порт контейнера
Пример:
```bash
sudo docker run -d \
	-p 5432:5432
        --name my-postgres \
        -e POSTGRES_PASSWORD=mysecretpassword \
        -e PGDATA=/var/lib/postgresql/data/pgdata \
        -v /home/postgres_mount:/var/lib/postgresql/data \
        postgres
```
