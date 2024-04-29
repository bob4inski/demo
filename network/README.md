# ЧТо нужно вообще сделать
1. Подключиться к оборудованию
2. Настроить OSPF на ELTEX потом на MICROTIK
3. Проверить работу OSPF
4. Настроить DHCP на MICROTIK
5. Настроить NAT

Описание сети
У нас есть 2 микротика - BR и HQ
И есть eltex
# 1 Подключение
Для подключения делаем следущее:
```bash
sudo yum install putty
sudo yum remove brltty
sudo putty
```

После этого вы видите терминал putty
В терминале выставляете serial и пишите /dev/ttyUSB0
и выставляете скорость 115200

Дефолт логин пароль:
eltex admin:password
mikrotik admin и пустой пароль

ПЕРЕД ТЕМ КАК ЧТО-ТО ДЕЛАТЬ НА МИКРОТИКЕ СДЕЛАЙТЕ СБРОС КОНФИГУРАЦИИ
команда для сброса ```system reset-configuration```
И после сброса надо будет ввести букву r чтобы был пустой микротик

# 2 OSPF
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

Потом на каждом порту, который подключен к микротику:
Порт 1 (номер интрфейса сами посмотрите какой подключен в строну микротика):
В первой строке номер порта
```
int gi1/0/3 
mode routerport 
des TO_MIKROTIK_HQ
ip address 10.10.1.1/24
ip ospf instance 10
ip ospf area 10.10.0.0
ip ospf
ip firewall disable
exit
```
Порт 2:
```
int gi1/0/5 
mode routerport 
des TO_MIKROTIK_BR
ip address 10.10.1.2/24
ip ospf instance 10
ip ospf area 10.10.0.0
ip ospf
ip firewall disable
exit
```

## На Mikrotik
### на первом микротике HQ (ELTEX подключен к 3 порт микротика, а в 9 и 10 будут поключены компы)
address - это адрес сети в первом случае это 10.10.1.0/24

В одну сторону у нас идет ELTEX с адресов 10.10.1.1
А в другую Mikrotik с адресом 10.10.1.2 (порт ether1)

```
/ip address add address=10.10.3.2/24 interface=ether3
/routing ospf instance set default redistribute-static=as-type-1  redistribure-connected=as-type-1 redistribute-rip=as-type-1 router-id=0.0.0.0
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
/interface bridge port add name=dhcp_hq 
/interface bridge port add bridge=dhcp_hq interface=ether9
/interface bridge port add bridge=dhcp_hq interface=ether10
/ip address add address 10.10.3.17/28 interface=dhcp_hq network=10.10.3.16

/ip pool add name=HQ_pool ranges=10.10.3.17-10.10.3.30
/ip dhcp-server add address-pool=HQ_pool disabled=no interface=dhcp_hq name=HQ
/ip dhcp-server network add address=10.10.3.16/28 gateway=10.10.3.17 netmask=28

```


На BR:
```
/interface bridge port add name=dhcp_BR 
/interface bridge port add bridge=dhcp_BR interface=ether9
/interface bridge port add bridge=dhcp_BR interface=ether10
/ip address add address 10.10.6.17/28 interface=dhcp_BR network=10.10.6.16

/ip pool add name=BR_pool ranges=10.10.6.17-10.10.6.30
/ip dhcp-server add address-pool=BR_pool disabled=no interface=dhcp_BR name=BR
/ip dhcp-server network add address=10.10.6.16/28 gateway=10.10.6.17 netmask=28

```

### Очистка nat
```
ip nat source
  no ruleset SNAT
  no pool
exit
commit
confirm
int gi1/0/3
  no ip nat proxy-arp PUBLIC
exit
no security zone-pair INTERNET LOCALSIRIUS
no security zone INTERNET
no security zone LOCALSIRIUS
no object-group network LOCAL
no object-group network PUBLIC
```
