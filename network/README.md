# Что нужно вообще сделать?
1. Подключиться к оборудованию
2. Настроить OSPF на ELTEX потом на MICROTIK
3. Проверить работу OSPF
4. Настроить DHCP на MICROTIK
5. Настроить NAT

Описание сети:
- 2 микротика - BR и HQ
- Eltex

# 1. Подключение
Подключение будет производиться через Putty:
```bash
sudo yum install putty
sudo yum remove brltty
sudo putty
```

В открытом терминале Putty:
1) В терминале выставить serial и установить serial line: /dev/ttyUSB0
2) Выставить скорость 115200
3) Во вкладке FONTS изменить шрифт 


На пустом маршрутизаторе при входе установлены дефолт логин пароль:
- (eltex) admin:password
- (mikrotik) admin и пустой пароль

После первичного подключения к элтексу необходимо произвести смену пароля:
password <тут пароль123>
commit
confirm

ПЕРЕД ТЕМ КАК ЧТО-ТО ДЕЛАТЬ НА МИКРОТИКЕ СДЕЛАЙТЕ СБРОС КОНФИГУРАЦИИ
команда для сброса ```system reset-configuration```
!!!!!!!! После сброса надо будет ** ввести букву r ** (чтобы был пустой микротик)

# 2. OSPF
## На ELTEX
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
## Конфигурируем порты Eltex

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
ip address 10.10.5.2/24
ip ospf instance 10
ip ospf area 10.10.0.0
ip ospf
ip firewall disable
exit
```
Заботливо напоминаем, что перед любыми манипуляциями на Mikrotik необходимо произвести сброс конфигурации:
- Введите команду ```system reset-configuration```
- Нажмите буковку "r"
## На Mikrotik
### на первом микротике HQ (ELTEX подключен к 3 порт микротика, а в 9 и 10 будут поключены компы)
address - это адрес сети в первом случае это 10.10.1.0/24


```
/ip address add address=10.10.3.2/24 interface=ether3
втыкаем 1 порт в интернет и прописываем команду
/ip dhcp-client add disabled=no interface=ether1
/ip firewall nat add action=masquerade chain=srcnat out-interface=ether1 src-address=10.10.0.0/16
Вот этой командой сверху мы включаем нат чтобы интернет работал

/routing ospf instance set default  distribute-default=always-as-type-1 redistribute-static=as-type-1  redistribure-connected=as-type-1 redistribute-rip=as-type-1 router-id=0.0.0.0
/routing ospf area add name=backbone0 area-id=10.10.0.0
/routing ospf network add area=backbone0 network=10.10.0.0/16

!проверка что работает
/routing ospf neighbor print
```
### На втором BR также но поменять address и gateway на (ELTEX подключен к 5 порту!)
```
/ip address add address=10.10.5.2/24 interface=ether5
/routing ospf instance set default redistribute-static=as-type-1  redistribure-connected=as-type-1 r>
/routing ospf area add name=backbone0 area-id=10.10.0.0
/routing ospf network add area=backbone0 network=10.10.0.0/16
/routing ospf neighbor print
```

## DHCP на микротах
На HQ:
Командой bridge port add мы обьединяем в 1 логический интерфейс 2 физических! 
То есть порты 9 и 10 будут для подключения компов и выдаче адресов по DHCP
```
/interface bridge add name=dhcp_hq 
/interface bridge port add bridge=dhcp_hq interface=ether9
/interface bridge port add bridge=dhcp_hq interface=ether10
/ip address add address 10.10.3.17/28 interface=dhcp_hq network=10.10.3.16

/ip pool add name=HQ_pool ranges=10.10.3.17-10.10.3.30
/ip dhcp-server add address-pool=HQ_pool disabled=no interface=dhcp_hq name=HQ
/ip dhcp-server network add address=10.10.3.16/28 gateway=10.10.3.17 netmask=28
```


На BR:
```
/interface bridge add name=dhcp_BR 
/interface bridge port add bridge=dhcp_BR interface=ether9
/interface bridge port add bridge=dhcp_BR interface=ether10
/ip address add address 10.10.5.17/28 interface=dhcp_BR network=10.10.5.16

/ip pool add name=BR_pool ranges=10.10.5.17-10.10.5.30
/ip dhcp-server add address-pool=BR_pool disabled=no interface=dhcp_BR name=BR
/ip dhcp-server network add address=10.10.5.16/28 gateway=10.10.5.17 netmask=28
```




