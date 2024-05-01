

# NFS

Как настроить NFS 


1. На СЕРВЕРЕ с NFS допустим (HQ)

sudo mkdir -m 777 /Branch_Files /Network /Admin_Files  

sudo nano /etc/exports

```
# вставляем это все в открытый файл и сохраняем изменения
/Branch_Files *(rw,sync)
/Network *(rw,sync)
/Admin_Files *(rw,sync)
```

И запускаем сервер 
```bash
sudo systemctl start nfs-kernel-server.service
sudo exportfs -a
```

Смотрим какой адрес у нашего сервера командой ip a
```txt
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether fa:16:3e:53:20:1b brd ff:ff:ff:ff:ff:ff
    altname enp0s3
он тут ----->>   inet 10.0.0.10/24 metric 100 brd 10.0.0.255 scope global dynamic ens3
       valid_lft 599377sec preferred_lft 599377sec
    inet6 fe80::f816:3eff:fe53:201b/64 scope link 
       valid_lft forever preferred_lft forever
```

1. На клиентском сервере (BR)

```bash
#создаем папки, в которые будем организовывать как обменник
sudo mkdir /mnt
sudo mkdir /mnt/Branch_Files /mnt/Network /mnt/Admin_Files
```


```bash
sudo apt install nfs-common
sudo nano /etc/fstab

#Вставить в открывшийся файлик для постоянного монтирования 
<ip nfs server>:/Branch_Files /mnt/Branch_Files nfs rsize=8192,wsize=8192,timeo=14,intr
<ip nfs server>:/Network /mnt/Network nfs rsize=8192,wsize=8192,timeo=14,intr
<ip nfs server>:/Admin_Files /mnt/Admin_Files nfs rsize=8192,wsize=8192,timeo=14,intr
```
После чего нужно сделать 
sudo mount -a

Для проверки надо зайти на BR в папку /mnt/Network 
И попробовать создать файл
cd /mnt/Network
touch 1.txt

Далее зайти на HQ в папку /Network
И посмотреть есть ли файлы внутри

ls /Network

