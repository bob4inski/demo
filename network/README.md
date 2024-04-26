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
mikrotik admin:

# 2 OSPF
## На ELTEX
```
router ospf 10
area 10.10.0.0
enable
exit
redistribute rip
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
### на первом микротике HQ
address - это адрес сети в первом случае это 10.10.1.0/24

В одну сторону у нас идет ELTEX с адресов 10.10.1.1
А в другую Mikrotik с адресом 10.10.1.2 (порт ether1)

```
/ip address add address=10.10.1.2/24 interface=ether1
/ip route add dst-address=0.0.0.0/0 gateway=10.10.1.1
/routing ospf instance set default redistribute-static=as-type-1 router-id=0.0.0.0
/routing ospf area add name backbone0 area-id=10.10.0.0
/routing ospf network add area=backbone0 network=10.10.0.0/16
```
### На втором BR также но поменять address и gateway
```
/ip address add address=10.10.2.2/24 interface=ether2
/ip route add dst-address=0.0.0.0/0 gateway=10.10.2.1
/routing ospf instance set default redistribute-static=as-type-1 router-id=0.0.0.0
/routing ospf area add name backbone0 area-id=10.10.0.0
/routing ospf network add area=backbone0 network=10.10.0.0/16
```

## DHCP на микротах
На HQ:
```
/interface bridge port print 
- смотрим под каким номером ether10
/interface bridge remove <номер порта который будет подключен ноут>
/ip address add address=10.10.3.1/24 interface=ether10 
/ip pool add name=br_network ranges=10.10.3.2-10.10.3.15
/ip dchp-server add address-pool=br_network interface=ether10 name=bd_dhcp
```

На BR:
```
/interface bridge port print 
- смотрим под каким номером ether10
/interface bridge remove <номер порта который будет подключен ноут>
/ip address add address=10.10.3.1/24 interface=ether10 
/ip pool add name=br_network ranges=10.10.3.2-10.10.3.15
/ip dchp-server add address-pool=br_network interface=ether10 name=bd_dhcp
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



