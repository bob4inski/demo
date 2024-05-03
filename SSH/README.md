# Настройте SSH на всех Linux хостах:
a. Banner ( Authorized access only! );
b. Установите запрет на доступ root;
c. Отключите аутентификацию по паролю;
Ну если отключим, то не сможем подключаться по SSH
d. Переведите на нестандартный порт;
e. Ограничьте ввод попыток до 4;
f. Отключите пустые пароли;
g. Установите предел времени аутентификации до 5 минут;

Сначала заходим на первый сервер и пробуем подключиться ко второму серверу:

sudo apt install -y openssh-server openssh-client

sudo adduser admin

Потом переходим на второй сервер и пробуем подключиться по ssh к первому 
ssh admin@<ip первого компа>

Как только зашли пишем exit
и пишем команды: 
ssh-keygen
(тут надо будет найти "your public key has been saved in ..." и вставить путь в команду ниже)
ssh-copy-id -i <путь из паблик кей>  <пользователь>@<ip первого компа>

Потом также повторяем со второго сервера на первый!

Открываем файл /etc/ssh/sshd_config командой 
```
sudo nano /etc/ssh/sshd_config
```

В файл записываем самое начало 

```txt
Banner /etc/ssh/banner     #задание а
PermitRootLogin no         #задание b
PasswordAuthentication yes #задание c
Port 13022                 #задание d
MaxAuthTries 4             #задание e
PermitEmptyPasswords no    #задание f
LoginGraceTime 5m          #задание g
```

После этого перезапуск сервиса SSH

```bash
sudo systemctl restart ssh sshd
```
