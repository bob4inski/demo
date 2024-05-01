## Подготовка сервера
На сервере нужно, предварительно, выполнить следующие настройки.
Время
Для правильной фиксации момента события в логе, необходимо настроить синхронизацию времени.
Сначала задаем правильный часовой пояс:
```bash
cp /usr/share/zoneinfo/Europe/Moscow /etc/localtime
#* в данном примере мы использовали московское время.
#Затем устанавливаем и запускаем chrony.
#на системе Ubuntu / Debian:
apt-get install chrony
systemctl enable chrony
systemctl start chrony
```
### Брандмауэр
Если используется брандмауэр, необходимо открыть порты TCP/UDP 514.
    a) с помощью firewalld:
```bash
firewall-cmd --permanent --add-port=514/{tcp,udp}
firewall-cmd –reload
```
   b) с помощью iptables:
```bash
iptables -A INPUT -p tcp --dport 514 -j ACCEPT
iptables -A INPUT -p udp --dport 514 -j ACCEPT
```
    c) с помощью ufw:
```bash
ufw allow 514/tcp
ufw allow 514/udp
ufw reload
```
## SELinux

```bash
#Проверяем, работает ли в нашей системе SELinux:
Getenforce
#Если мы получаем в ответ:
Enforcing
#Тогда необходимо либо настроить SELinux:
semanage port -m -t syslogd_port_t -p tcp 514
semanage port -m -t syslogd_port_t -p udp 514
#либо отключить его командами:
setenforce 0
sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config
```

# Установка и запуск rsyslog

```bash
sudo apt-get install -y rsyslog
Открываем конфигурационный файл:
sudo nano /etc/rsyslog.conf

Снимаем комментарии со следующих строк:
$ModLoad imudp
$UDPServerRun 514
$ModLoad imtcp
$InputTCPServerRun 514

#* в данном примере мы разрешили запуск сервера для соединений TCP и UDP на портах 514. На самом деле, можно оставить только один протокол, например, более безопасный и медленный TCP.
```

```bash
#После добавляем в конфигурационный файл строки:
$template RemoteLogs,"/var/log/rsyslog/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?RemoteLogs
& ~
#в данном примере мы создаем шаблон с названием RemoteLogs, который принимает логи всех категорий, любого уровня (про категории и уровни читайте ниже); логи, полученный по данному шаблону будут сохраняться в каталоге по маске /var/log/rsyslog/<имя компьютера, откуда пришел лог>/<приложение, чей лог пришел>.log; конструкция & ~ говорит о том, что после получения лога, необходимо остановить дальнейшую его обработку.
```

```bash
#Перезапускаем службу логов:
systemctl restart rsyslog
```

# Настройка клиента

## Устанавливаем rsyslog и настраиваем его

```bash
sudo apt-get install -y rsyslog
#Открываем конфигурационный файл:
sudo nano /etc/rsyslog.conf

#Снимаем комментарии со следующих строк:
$ModLoad imudp
$UDPServerRun 514
$ModLoad imtcp
$InputTCPServerRun 514

#* в данном примере мы разрешили запуск сервера для соединений TCP и UDP на портах 514. На самом деле, можно оставить только один протокол, например, более безопасный и медленный TCP.
```

```bash
#Для начала можно настроить отправку всех логов на сервер. Создаем конфигурационный файл для rsyslog:
nano /etc/rsyslog.d/all.conf
#Добавляем:
*.* @@<ip address>:514
# где ip address это адрес сервера rsyslog
#Перезапускаем rsyslog на клиенте
sudo systemctl restart rsyslog
```

# Проверка 

```bash
ls /var/log/rsyslog/
# вывод должен быть примерно таким
# и в каждой папке есть логи каждого сервера
server-br  server-hq
```



