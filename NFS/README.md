

# NFS

## На сервере HQ

1. Пусть сервером с NFS будет компьютер HP, подключенный к маршрутизатору Mikrotik HQ. В терминале необходимо прописать команды:
```
sudo mkdir -m 777 /Branch_Files /Network /Admin_Files `  
sudo nano /etc/exports
```
2. В открытый файл /etc/exports, необходимо занести следующие записи и сохраните изменения (ctrl+x, yes, enter):
```   
/Branch_Files *(rw,sync)
/Network *(rw,sync)
/Admin_Files *(rw,sync)
```

3. Запустите сервер:
```bash
sudo systemctl start nfs-kernel-server.service
sudo exportfs -a
```

4. Командой ip a посмотрите адрес сервера (в данном примере это 10.0.0.10):
```txt
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether fa:16:3e:53:20:1b brd ff:ff:ff:ff:ff:ff
    altname enp0s3
он тут ----->>   inet 10.0.0.10/24 metric 100 brd 10.0.0.255 scope global dynamic ens3
       valid_lft 599377sec preferred_lft 599377sec
    inet6 fe80::f816:3eff:fe53:201b/64 scope link 
       valid_lft forever preferred_lft forever
```

## На сервере BR (клиент)

1. Необходимо создать папки, в которые будут организованы, как обменник
```bash
sudo mkdir /mnt
sudo mkdir /mnt/Branch_Files /mnt/Network /mnt/Admin_Files
```

2. Введите следующие команды и в открывшемся файле вставьте записи для постоянного монтирования. Вместо ip nfs server вставьте адрес, полученный в п.4 с помощью команды ip a
```bash
sudo apt install nfs-common
sudo nano /etc/fstab

<ip nfs server>:/Branch_Files /mnt/Branch_Files nfs rsize=8192,wsize=8192,timeo=14,intr
<ip nfs server>:/Network /mnt/Network nfs rsize=8192,wsize=8192,timeo=14,intr
<ip nfs server>:/Admin_Files /mnt/Admin_Files nfs rsize=8192,wsize=8192,timeo=14,intr
```
3. Пропишите команду ` sudo mount -a `

## Проверка

1. На сервере BR зайдите в папку /mnt/Network с помощью команды `cd /mnt/Network
2. Создайте файл с помощью команды `touch 1.txt`
3. На сервере HQ перейдите в папку /Network с помощью команды `ls /Network`
4. Убедитесь, что в директории есть файл


