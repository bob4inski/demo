# Настройте SSH на всех Linux хостах:
a. Banner ( Authorized access only! );
b. Установите запрет на доступ root;
c. Отключите аутентификацию по паролю;
Ну если отключим, то не сможем подключаться по SSH
d. Переведите на нестандартный порт;
e. Ограничьте ввод попыток до 4;
f. Отключите пустые пароли;
g. Установите предел времени аутентификации до 5 минут;


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