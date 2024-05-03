/ip firewall filter
```
#разрешаем icmp
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=icmp

#разрешаем ospf
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=ospf

#разрешаем dns 53 порт
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=tcp port=53
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=udp port=53


#разрешаем 80 порт
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=tcp port=80
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=udp port=80

#разрешаем 8080 порт
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=tcp port=8080
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=udp port=8080

#разрешаем 8081 порт
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=tcp port=8081
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=udp port=8081

#разрешаем 22 порт
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=tcp port=22
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=udp port=22

#разрешаем 13022 порт
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=tcp port=13022
add chain=input action=accept src-address=10.10.0.0/16 dst-address=10.10.0.0/16 protocol=udp port=13022

```
