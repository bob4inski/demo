#HQ-R
/interface bridge
add name=hq-lo0

/ip address
add address=192.168.0.1 interface=hq-lo0

/system ntp client
set enabled=yes primary-ntp=162.159.200.1
/system ntp server
set enabled=yes
/system clock
set time-zone-name=Europe/Moscow

#BR-R
/system ntp client
set enabled=yes primary-ntp=192.168.0.1
/system clock
set time-zone-name=Europe/Moscow

#ELTEX
clock timezone gmt +3

ntp enable
ntp broadcast-client enable
ntp server 192.168.0.1
exit

#Ubuntu
sudo apt -y intsall nano systemd-timesyncd
sudo bash -c 'echo "NTP=192.168.0.1" >> /etc/systemd/timesyncd.conf'
sudo systemctl restart systemd-timesyncd
sudo timedatectl set-timezone "Europe/Moscow"
