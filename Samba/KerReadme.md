 На сервере с папками прописываем 
mkdir /Branch_Files /Network /Admin_Files

Выдаем права на папки

sudo chmod 770 /Branch_Files
sudo chmod 770 /Network
sudo chmod 770 /Admin_Files

Создаем группы
sudo groupadd file_admin
sudo groupadd network_admin
sudo groupadd branch_admin

Создаем юзеров
sudo adduser --no-create-hom --shell /usr/sbin/nologin user1
sudo adduser --no-create-hom --shell /usr/sbin/nologin user2
sudo adduser --no-create-hom --shell /usr/sbin/nologin user3

Добавляем пользователей в группу
sudo usermod -G network_admin user1
sudo usermod -G file_admin user2
sudo usermod -G branch_admin user3

Установка самба
sudo apt update -y
sudo apt install samba -y

Меняем конфиг
Переходим в sudo nano /etc/samba/smb.conf
Добавляем строки

[Network]
   path = /Network
   valid users = @network_admin
   write list = @network_admin
   read only = no

[Admin_Files]
   path = /Admin_Files
   valid users = @file_admin
   write list = @file_admin
   read only = no

[Branch_Files]
   path = /Branch_Files
   valid users = @branch_admin
   write list = @branch_admin
   read only = no

Меняем права на папки для групп пользователей
sudo chown root:file_admin /Admin_Files
sudo chown root:branch_admin /Branch_Files
sudo chown root:network_admin /Network

Ребут этого говна всего
sudo service smbd restart
sudo service smbd restart
