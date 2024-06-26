    https://github. com/ abdurrah1m/DEMO2024

    h[ttp://wiki. pantsuh.ru/ ru/Demo-2024 /задание1-1](http://wiki.pantsuh.ru/ru/Demo-2024/задание1-1)
[решебник к демке.docx](https://github.com/borpov/d/files/15465237/default.docx)

1. Выполните базовую настройку всех устройств:

![image](https://github.com/DimaDanil/demo/assets/170974285/57b82acd-c3be-44a9-95ed-798048279242)

[root@localhost ~]# hostnamectl set-hostname <NAME>

[root@localhost ~]# exec bash

Для устройств BR-SRV и CLI желательно сразу установить полное доменное имя. Потребуется для ввода этих машин в домен во второй части задания.

Например:
ISP: isp

CLI: cli.hq.work

HQ-R: hq-r.hq.work

HQ-SRV: hq-srv.hq.work

BR-R: br-r.branch.work

BR-SRV: br-srv.branch.work
_____________
На интерфейсах, которые смотрят в сторону интернета ОБЯЗАТЕЛЬНО нужно настроить "Серверы DNS", для доступа к сервисам по доменным именам, а не ip-адресам

        nmtui
![image](https://github.com/DimaDanil/demo/assets/170974285/e44b609f-3dfd-4f41-9dbf-85964414762a)

systemctl restart networking (NetworkManager)

![image](https://github.com/DimaDanil/demo/assets/170974285/73b5acb9-ef1e-49c1-b1a9-c71c8e427060)

![image](https://github.com/DimaDanil/demo/assets/170974285/df5def94-c74b-4e84-87f2-8ac7fc3ac4d2)

___________________________________________________________________________________________

![image](https://github.com/borpov/d/assets/170978905/ada8f1f3-be6a-4ed5-a183-76fb404d29b5)

    nano /etc/apt/sources.list

Прописываем:

1

    deb http://deb.debian.org/debian bookworm main non-free-firmware contrib non-free
    deb-src http://deb.debian.org/debian bookworm main non-free-firmware

2 

    deb http://deb.debian.org/debian-security bookworm-security main non-free-firmware contrib non-free
    deb-src http://deb.debian.org/debian-security bookworm-security main non-free-firmware
3

    deb http://deb.debian.org/debian bookworm-updates main non-free-firmware contrib non-free
    deb-src http://deb.debian.org/debian bookworm-updates main non-free-firmware

___________________________________________________________________________________________
Маршрутизация транзитных IP-пакетов

    nano /etc/sysctl.conf

В данном файле прописываем следующие строки:

   net.ipv4.ip_forward=1

   net.ipv6.conf.all.forwarding=1

    sysctl -p

Настройка nftables на ISP
Перед установкой необходимо убедиться что имеется доступ в интернет с ВМ ISP

    ping -c4 ya.ru

    dnf install -y nftables

После установки:

    nano /etc/nftables/isp.nft

Прописываем следующие строки:

    table inet my_nat {
    
        chain my_masquerade {
        
        type nat hook postrouting priority srcnat;
        
        oifname "ens18" masquerade
        
        }
        
    }

где ens18 - публичный интерфейс ISP (смотрящий в Интернет)
После:

    nano /etc/sysconfig/nftables.conf

Ниже строки начинающейся на include, прописываем строку

    include "/etc/nftables/isp.nft"

    systemctl enable --now nftables
    
Настройка на HQ-R

     dnf install -y nftables
     
     nano /etc/nftables/hq-r.nft

    table inet my_nat {
    
        chain my_masquerade {
        
        type nat hook postrouting priority srcnat;
        
        oifname "ens18" masquerade
        
        }
    }


    nano /etc/sysconfig/nftables.conf
    
    include "/etc/nftables/hq-r.nft"
    
    systemctl enable --now nftables

    Настройка nftables на  BR-R

    dnf install -y nftables

    nano /etc/nftables/br-r.nft

  Настройка nftables на  BR-R

    dnf install -y nftables

    nano /etc/nftables/br-r.nft

  Прописываем

    table inet my_nat {
    
        chain my_masquerade {
        
        type nat hook postrouting priority srcnat;
         
        oifname "ens18" masquerade
        
        }
     }

    nano /etc/sysconfig/nftables.conf
    
    include "/etc/nftables/br-r.nft"

    systemctl enable --now nftables




_______________________________________________________________________________________________
2. Настроика внутренней динамической маршрутизации по средствам FRR.

Настройка HQ-R

![image](https://github.com/DimaDanil/demo/assets/170974285/b7b3f101-4faa-475a-91bd-0289802b275e)

![image](https://github.com/DimaDanil/demo/assets/170974285/6055d22d-0b7a-4be2-87d5-cf1edb2d735e)

Настройка BR-R
 Меняем локал и удал IP местами и IP будеи из 2 табл

       nmcli connection modify tun1 ip-tunnel.ttl 64

Настройка динамической (внутренней) маршрутизации средствами FRR

Установка пакет frr
      dnf install -y frr

    nano /etc/frr/daemons


Настройка nftables на  BR-R

    dnf install -y nftables

    nano /etc/nftables/br-r.nft

  Прописываем

    table inet my_nat {
    
        chain my_masquerade {
        
        type nat hook postrouting priority srcnat;
         
        oifname "ens18" masquerade
        
        }
     }

    nano /etc/sysconfig/nftables.conf
    
    include "/etc/nftables/br-r.nft"

    systemctl enable --now nftables

Настройку динамическое маршрутизации производим с помощью протокола OSPF – Данный протокол динамической сети позволяет разделять сеть на логические области, что делает его масштабируемым для больших сетей.
Каждая область может иметь свою таблицу маршрутизации, что уменьшает нагрузку на маршрутизаторы и улучшает производительность сети.
_______________________________________________________________________________________________
2. Настроика внутренней динамической маршрутизации по средствам FRR.

Настройка HQ-R

![image](https://github.com/DimaDanil/demo/assets/170974285/b7b3f101-4faa-475a-91bd-0289802b275e)

![image](https://github.com/DimaDanil/demo/assets/170974285/6055d22d-0b7a-4be2-87d5-cf1edb2d735e)

Настройка BR-R
 Меняем локал и удал IP местами и IP будеи из 2 табл

       nmcli connection modify tun1 ip-tunnel.ttl 64

Настройка динамической (внутренней) маршрутизации средствами FRR

Установка пакет frr
      dnf install -y frr

    nano /etc/frr/daemons
В самом файле меняем:

ospfd = yes - для OSPFv2 (IPv4)

ospf6d = yes - для OSPFv3 (IPv6)

Включаем и добавляем в автозагрузку службу FRR:

    systemctl enable --now frr

    vtysh
![image](https://github.com/borpov/demo/assets/170978905/a493f191-2803-425e-9f06-030a8c015462)

Схема L3:

![image](https://github.com/borpov/demo/assets/170978905/89c30468-7583-4084-85e1-824b74a9e16b)

_____________________________________________________________________________________________

  3. Установка DHCP. Настройка DHCP на HQ-R для IPv4

    dnf install  dhcp-server
    
    nano /etc/dhcp/dhcpd.conf


    subnet 172.16.100.0 netmask 255.255.255.192 {

    range 172.16.100.2 172.16.100.62;
  
    option routers 172.16.100.1;

    default-lease-time 600;

  
    max-lease-time 7200;
    }

    Резервирование ip-адреса за клиентом
    
Резервирование ip-адреса за клиентом: Хосту с именем HQ-SRV , у которого сетевая карта имеет MAC ff:ff:ff:ff:ff:ff зарезервируем адрес 172.16.100.2.

    host HQ-SRV {

        hardware ethernet ff:ff:ff:ff:ff:ff;
        
        fixed-address 172.16.100.2;
    }

Открываем файл конфигурации

    nano /etc/sysconfig/dhcpd

Добавляем в него следующее:

    DHCPDARGS=ens19
    
Запускаем и добавляем в автозагрузку службу dhcpd (для IPv4):

    systemctl enable --now dhcpd

Настройка DHCP на HQ-R для IPv6

![image](https://github.com/borpov/demo/assets/170978905/78ec05d6-4e95-4e6b-af6d-c3521527e338)

    cp /usr/share/doc/dhcp-server/dhcpd6.conf.example /etc/dhcp/dhcpd6.conf

    nano /etc/dhcp/dhcpd6.conf
 
![image](https://github.com/borpov/demo/assets/170978905/5a7dba20-4ce5-4c09-9b3b-57968e349702)

    systemctl enable --now dhcpd6  Перезагружаем сетевой интерфейс на HQ-SRV
    
Далее пропписываем:

    journalctl -f -u dhcpd6.service

 ![image](https://github.com/borpov/demo/assets/170978905/5283e6cf-d377-4056-a567-1a73618341bd)

    systemctl restart dhcpd6

  Установка и настройка RA

    dnf install -y radvd

    nano /etc/sysctl.conf

  Добавляем: net.ipv6.conf.enp0s8.accept_ra=2

  ![image](https://github.com/borpov/demo/assets/170978905/f496a33d-fd2d-43d2-924a-a815f7d6ec53)

    systemctl restart dhcpd6

    systemctl enable --now radvd

_______________________________________________________________________________________________
    
4. Настроить локальные учётные записи

       useradd -c "Admin" admin -U / useradd -c "Network admin" network_admin -U
   
       passwd admin / passwd network_admin

admin - имя пользователя

-c Admin любая текстовая строка.
-U - cоздание группы с тем же именем

_______________________________________________________________________________________________

5.Измерить пропускную способность сети

    dnf install iperf3 -y

машина ISP: iperf3 -s и машина HQ-R: iperf3 -c IP_address_ISP

_______________________________________________________________________________________________

6. Составить backup скриптов


Создадим директорию для хранения скрипта резервного копирования backup-script и директорию для хранения архивов резервных копий backup на машине HQ-R 

       mkdir /var/{backup,backup-script}

       nano /var/backup-script/backup.sh

![image](https://github.com/borpov/demo/assets/170978905/bb10ae7d-3d6c-4afd-9903-1ef300a74010)

    chmod +x /var/backup-script/backup.sh

    /var/backup-script/backup.sh (запуск скрипта)

    mkdir /var/{backup,backup-script} (перенос на BR-R)

  BR-R

    scp admin@10.10.10.1:/var/backup-script/backup.sh /var/backup-script/

Если потребует права, то: chmod +x /var/backup-script/backup.sh

    /var/backup-script/backup.sh (запуск скрипта)
_______________________________________________________________________________________________

7. Настройте подключение по SSH

       nano /etc/ssh/sshd_config
   И меняем на Port 2222

       systemctl restart sshd

Перенаправление

    nano etc/nftable/hq-r.nft

![image](https://github.com/borpov/demo/assets/170978905/8fc908d2-1857-45d2-822d-0b87df41b707)

    systemctl restart nftables

   _______________________________________________________________________________________________
  
  8.Настройте контроль доступа до HQ-SRV по SSH
  
     dnf install -y nftables

     nano /etc/nftables/hq-srv.nft

Далее прописываем

    table inet filter {
    
            chain input {
            
            type filter hook input priority filter; policy accept;
            
            ip saddr 3.3.3.2 tcp dport 2222 counter reject
            
            ip saddr 4.4.4.0/30 tcp dport 2222 counter reject
            
            ip6 saddr 2024:3::/64 tcp dport 2222 counter reject
            
            ip6 saddr 2024:4::/64 tcp dport 2222 counter reject
            
            }
            
     }

Запуск и добавление в автозагрузку сервиса
     
     systemctl enable --now nftables
