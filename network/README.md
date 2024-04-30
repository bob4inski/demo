# Что нужно вообще сделать?
1. Подключиться к оборудованию
2. Настроить OSPF на ELTEX потом на MICROTIK
3. Проверить работу OSPF
4. Настроить DHCP на MICROTIK
5. Настроить NAT

### Описание сети, вводные данные:
- 2 Mikrotik - BR и HQ (дефолтный логин-пароль : admin- (пустой пароль))
- Eltex (дефолтный логин-пароль : admin-password)

# Подключение
Подключение будет производиться через Putty, для его установки:
```bash
sudo apt install putty
sudo apt remove brltty
sudo putty
```

В открытом терминале Putty:
1) В терминале выставить serial и установить serial line: /dev/ttyUSB0
2) Выставить скорость 115200
3) Во вкладке FONTS изменить шрифт
4) Нажать OPEN


# Заходим на ELTEX (для этого консольник втыкаем в Eltex):
1) На пустом маршрутизаторе при входе установлены дефолт логин пароль:
- (eltex) admin:password

2) После первичного подключения к Eltex необходимо произвести смену пароля:
```
password <тут пароль123>
commit
confirm


зайти в конфигурационный режим
configure 
```

## 1. Настройка OSPF на Eltex:
- вся настройка производится в режиме конфигурации, для этого необходимо ввести команду `configure`

```
router ospf 10
area 10.10.0.0
enable
exit
redistribute rip
redistribute connected
redistribute static
enable
exit
```
## 2. Конфигурируем порты Eltex:

Порт 1:
```
int gi1/0/3 #в этой строчке указан номер порта Eltex, который подключен к Mikrotik1
mode routerport 
des TO_MIKROTIK_HQ
ip address 10.10.3.1/24
ip ospf instance 10
ip ospf area 10.10.0.0
ip ospf
ip firewall disable
exit
```
Порт 2:
```
int gi1/0/5 #в этой строчке указан номер порта Eltex, который подключен к Mikrotik2
mode routerport 
des TO_MIKROTIK_BR
ip address 10.10.5.1/24
ip ospf instance 10
ip ospf area 10.10.0.0
ip ospf
ip firewall disable
exit
```
Поздравляю! Выйдите из режима конфигурации с помощью команды `end` и сохраните конфигурацию с помощью команды `commit`, примените конфигурацию с помощью команды `confirm`

# Заходим на Mikrotik1 (для этого консольник вытыкаем из Eltex втыкаем в Mikrotik1):
1) Заботливо напоминаем, что перед любыми манипуляциями на Mikrotik необходимо произвести сброс конфигурации:
- Введите команду ```system reset-configuration```
- Нажмите буковку "r"
2) На пустом маршрутизаторе при входе установлены дефолт логин пароль:
  (дефолтный логин-пароль : admin- (пустой пароль))

## 1. Настройка на Mikrotik1 (он же HQ):

```
/system identity set name=HQ

# Конфигурация портов:
/ip address add address=10.10.3.2/24 interface=ether3
# Втыкаем 1 порт Mikrotik в интернет (кабель в пол) и прописываем команду:
/ip dhcp-client add disabled=no interface=ether1 

# Включаем НАТ для работы интернета:
/ip firewall nat add action=masquerade chain=srcnat out-interface=ether1 src-address=10.10.0.0/16

# Настройка  OSPF:
/routing ospf instance set default  distribute-default=always-as-type-1 redistribute-static=as-type-1  redistribure-connected=as-type-1 redistribute-rip=as-type-1 router-id=0.0.0.0
/routing ospf area add name=backbone0 area-id=10.10.0.0
/routing ospf network add area=backbone0 network=10.10.0.0/16

# Проверка, что OSPF работает:
/routing ospf neighbor print

# Настройка DHCP (командой bridge port add мы обьединяем в 1 логический интерфейс 2 физических, 
то есть порты 9 и 10 будут использоваться для подключения компов и выдаче адресов по DHCP):
/interface bridge add name=dhcp_hq 
/interface bridge port add bridge=dhcp_hq interface=ether9
/interface bridge port add bridge=dhcp_hq interface=ether10
/ip address add address= 10.10.3.17/28 interface=dhcp_hq network=10.10.3.16

/ip pool add name=HQ_pool ranges=10.10.3.17-10.10.3.30
/ip dhcp-server add address-pool=HQ_pool disabled=no interface=dhcp_hq name=HQ
/ip dhcp-server network add address=10.10.3.16/28 gateway=10.10.3.17 netmask=28


# Проверка, что DHCP работает:
- Подключите ноутбук консольным кабелем к порту микротика
- На ноутбуке напишите команду `ip a` 
- Если адрес с началом 10.10, то все ок

```
# Заходим на Mikrotik2 (для этого консольник вытыкаем из Mikrotik1 втыкаем в Mikrotik2):
1) Заботливо напоминаем, что перед любыми манипуляциями на Mikrotik необходимо произвести сброс конфигурации:
- Введите команду ```system reset-configuration```
- Нажмите буковку "r"
2) На пустом маршрутизаторе при входе установлены дефолт логин пароль:
  (дефолтный логин-пароль : admin- (пустой пароль))

## 2. Настройка на Mikrotik2 (он же BR) :
```
/system identity set name=BR

# Конфигурация портов:
/ip address add address=10.10.5.2/24 interface=ether5

# Настройка  OSPF:
/routing ospf instance set default redistribute-static=as-type-1  redistribure-connected=as-type-1 redistribute-rip=as-type-1 router-id=0.0.0.0
/routing ospf area add name=backbone0 area-id=10.10.0.0
/routing ospf network add area=backbone0 network=10.10.0.0/16

# Проверка, что OSPF работает:
/routing ospf neighbor print

# Настройка DHCP (командой bridge port add мы обьединяем в 1 логический интерфейс 2 физических, 
то есть порты 9 и 10 будут использоваться для подключения компов и выдаче адресов по DHCP)
/interface bridge add name=dhcp_BR 
/interface bridge port add bridge=dhcp_BR interface=ether9
/interface bridge port add bridge=dhcp_BR interface=ether10
/ip address add address 10.10.5.17/28 interface=dhcp_BR network=10.10.5.16

/ip pool add name=BR_pool ranges=10.10.5.17-10.10.5.30
/ip dhcp-server add address-pool=BR_pool disabled=no interface=dhcp_BR name=BR
/ip dhcp-server network add address=10.10.5.16/28 gateway=10.10.5.17 netmask=28
# Чтобы на втором микроте были днс сервера, необходимо выполнить следующую команду (чтобы браузер открылся)
/ip dns servers=77.88.8.88,77.88.8.2

# Проверка, что DHCP работает:
- Подключите ноутбук консольным кабелем к порту микротика
- На ноутбуке напишите команду `ip a` 
- Если адрес с началом 10.10, то все ок





