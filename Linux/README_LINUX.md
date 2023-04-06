## Настройка ip аддресов и сетевых параметров

apt install network-manager -y ; nano /etc/NetworkManager/NetworkManager.conf

reboot

## Выбор источник скачивания

nano /etc/apt/sources.list

apt update; apt list --upgradble

## NTP chrony

apt install chrony -y; nano /etc/chrony/chrony.conf

//Use Debian vendor zone указывем шлюз

systemctl restart chrony; systemctl status chrony

// Проверка

chronyc tracking

timedatectl

## Установка домена и часового пояса

hostnamectl set-hostname //Название машины + .<филиал>.jun39.wsr  PC-IKT.ikt.jun39.wsr

timedatectl set-timezons // Указываем соотвествующий часововой пояс Asia/Irkutsk

## LLDP

apt install lldpd -y; llpdctl

## Создание пользователя

apt install sudo -y

adduser admin

usermod -aG sudo admin

//Проверка

groups admin

## ssh

// VDS

apt install ssh -y; systemctl status sshd

su user

mkdir /home/user/.sshd

// PC-MSK

ssh-keygen

cd /home/user/.ssh

ls -l

scp id_rsa.pin user@100.70.6.12:/home/user/.ssh/authorized_keys

su root

nano /etc/hosts

exit

// Проверка

ssh user@VDS


## DHCP server SRV1-MSK

apt install isc-dhcp-server -y; nano /etc/default/isc-dhcp-server

//ipv6 блокируем, а ipv4 указываем начальную сеть 192

reboot

nano /etc/dhcp/dhcpd.conf

_____________________________

option domain-name "msk.jun39.wsr";
option domain-name-servers "192.168.10.1";

subnet <Своя сеть> mask <Маска подсети> {
}

subnet <Сеть которую мы хотим выдовать> mask <Маска подсети>{ // 192.168.11.0 255.255.255.0
    range <Минимальное выдоваемое ip> <Максимальный ip>;  //192.168.11.20 192.168.11.200
    option routers <Указываем шлюз>;  // 192.168.11.1
    option ntp-servers <Указываем ip аддрес ntp сервера>;  // 192.168.10.1
}
_____________________________

systemctl restart isc-dhcp-server; systemctl status isc-dhcp-server

## NFS сервер

apt install nfs-kernel-server

mkdir -p /opt/nfs/rw

mkdir -p /opt/nfs/ro

chmod a+w /opt/nfs/rw

chmod a+w /opt/nfs/ro

touch /opt/nfs/rw/testRW.txt

touch /opt/nfs/ro/testRO.txt

chown -R admin:admin /opt/nfs

nano /etc/exports

/opt/nfs/rw 192.168.3.0/24(rw,sync)

/opt/nfs/ro 192.168.3.0/24(ro,sync)

systemctl restart nfs-server

// Проверка

showmount -e 192.168.3.2

## NFS клиент

apt install nfs-common -y

mkdir -p /home/user/Desktop/nfs_rw

mkdir -p /home/user/Desktop/nfs_ro

nano /etc/fstab

_____________________________

192.168.3.2:/opt/nfs/rw/ /home/user/Desktop/nfs_rw nfs defaults 0 0

192.168.3.2:/opt/nfs/ro/ /home/user/Desktop/nfs_ro nfs defaults 0 0

_____________________________

mount -a

df -h

// Затем заходим на PC-IKT под user P@ssw0rd

## Установка web сервера

apt install nginx -y

touch /var/www/html/index.html

nano /var/www/html/index.html

_____________________________

Пишем что-то

_____________________________

systemctl restart nginx; systemctl status nginx

## DNS сервер

apt install bind9 -y

nano /etc/bind/named.conf

// Закоментируйте последную строчку

#include "/etc/bind/named.conf.default-zones";

nano /etc/bind/named.conf.options

_____________________________

_____________________________

systemctl restart bind9; systemctl status bind9

cp /etc/bind/db.local /var/cache/bind/jun39.wsr

nano /var/cache/bind/jun39.wsr

_____________________________

_____________________________

cp /var/cache/bind/jun39.wsr /var/cache/bind/ams.jun39.wsr

nano /var/cache/bind/ams.jun39.wsr

_____________________________

web A 100.70.3.45

_____________________________

chown bind:bind /var/cache/bind/*

ls -l /var/cache/bind/

nano /etc/bind/named.conf.local
// Если что смотреть в фотографиях подробно
systemctl restart bind9; systemctl status bind9
