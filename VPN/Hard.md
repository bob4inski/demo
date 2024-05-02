Настройка сервера Wireguard:
1. Скачать пакет wireguard
sudo apt install wireguard
2. Перейти в папку /etc/wireguard/ , сгенерировать приватный и публичный ключ wireguard командой:
wg genkey | tee /etc/wireguard/private.key | wg pubkey > /etc/wireguard/public.key
3. Создать конфигурационный файл сервера server.conf 
[Interface]
PrivateKey = your_private_server_key
Address = your.vpn.net.1/24
ListenPort = 1194
SaveConfig = false

# First client
[Peer]
PublicKey = your_public_client_key
AllowedIPs = your.vpn.net.2
4. Запустить сервис и добавить в автозагрузку
sudo systemctl start wg-quick@server
sudo systemctl enable wg-quick@server


Подключение клиента:
Типовой конфиг клиента:
[Interface]
PrivateKey = your_private_client_key
Address = your.vpn.ip/24 
 
[Peer]
PublicKey = your_public_server_key
AllowedIPs = your.vpn.net.0/24
Endpoint = your.server.ip:1194
PersistentKeepalive = 15

1. Скачать пакет wireguard
sudo apt install wireguard

2. Перейти в папку /etc/wireguard/ , сгенерировать приватный и публичный ключ wireguard командой:
wg genkey | tee /etc/wireguard/private.key | wg pubkey > /etc/wireguard/public.key

3. Создать конфигурационный файл клиента client.conf и вставить содержимое из типового конфига в начале. Вставить свой приватный ключ клиента wireguard и IP адрес. 

4. Зайти на ВПН-сервер и добавить свой хост в конфиг /etc/wireguard/server.conf.
sudo nano /etc/wireguard/server.conf
sudo systemctl restart wg-quick@server

5. Проверить подключение к серверу
sudo wg-quick up client
ping your.vpn.server.ip
ping 8.8.8.8
sudo wg-quick down client

6. Добавить в автозагрузку
sudo systemctl start wg-quick@client
sudo systemctl enable wg-quick@client