# Настройка автоматических функций безопасности
## Задание
### Часть 1: Настройка основных параметров устройства
#### Подключите сеть, как показано в топологии.
#### Настройте базовую IP-адресацию для маршрутизаторов и ПК.
#### Настройте маршрутизацию OSPF.
#### Настройте узлы ПК.
#### Проверьте подключение между хостами и маршрутизаторами.
### Часть 2: Настройка автоматических функций безопасности
#### Заблокируйте маршрутизатор с помощью автозащиты и проверьте конфигурацию.
#### Сравните использование автозащиты с защитой маршрутизатора вручную с помощью командной строки.
### Часть 1
#### Соединим и сконфигурируем сетевые устройства в соответствии с заданием
![](https://github.com/igorvrn/otus2/blob/main/001.png)
#### Выполним команды show ip ospf neighbor на роутерах
![](https://github.com/igorvrn/otus2/blob/main/002.png)
![](https://github.com/igorvrn/otus2/blob/main/003.png)
![](https://github.com/igorvrn/otus2/blob/main/004.png)
#### Выполним команды show ip route на роутерах
![](https://github.com/igorvrn/otus2/blob/main/005.png)
![](https://github.com/igorvrn/otus2/blob/main/006.png)
![](https://github.com/igorvrn/otus2/blob/main/007.png)
#### Проверим соединение между PC-A и PC-C
![](https://github.com/igorvrn/otus2/blob/main/008.png)
### Часть 2
#### Выполним настройки безопасности на роутерах и проверим их работу
![](https://github.com/igorvrn/otus2/blob/main/009.png)

### Ответы на вопросы:
#### 1. Были выполнены следующие команды, отключающие пересылку, ответы и т.д.:
 no ip redirects
 no ip proxy-arp
 no ip unreachables
 no ip directed-broadcast
 no ip mask-reply
#### 2. Была выполнена команда passive-interface для отключения маршрутизации на выбранных портах.
#### 3. Частично совпадает с 1 вопросом. 
Disabling the bootp server
Disabling the http server
Disabling the finger service
Disabling source routing
Disabling gratuitous arp

### Конфиг R1:
Current configuration : 1099 bytes
!
version 15.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
security passwords min-length 10
!
hostname R1
!
ip cef
no ipv6 cef
!
ip ssh version 2
ip ssh authentication-retries 2
ip ssh time-out 90
no ip domain-lookup
ip domain-name netsec.com
!
spanning-tree mode pvst
!
interface GigabitEthernet0/0/0
 ip address 10.1.1.1 255.255.255.252
 duplex auto
 speed auto
!
interface GigabitEthernet0/0/1
 ip address 192.168.1.1 255.255.255.0
 duplex auto
 speed auto
!
interface Vlan1
 no ip address
 shutdown
!
router ospf 1
 log-adjacency-changes
 passive-interface GigabitEthernet0/0/1
 network 192.168.1.0 0.0.0.255 area 0
 network 10.1.1.0 0.0.0.3 area 0
!
router rip
!
ip classless
!
ip flow-export version 9
!
banner motd ^C Unauthorized access is strictly prohibited! ^C
!
line con 0
 exec-timeout 5 0
 logging synchronous
 login local
!
line aux 0
 exec-timeout 5 0
 login local
!
line vty 0 4
 exec-timeout 5 0
 login local
 transport input ssh
 privilege level 15
!
!
!
end

### Конфиг R2
R2#show running-config
Building configuration...

Current configuration : 734 bytes
!
version 15.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R2
!
ip cef
no ipv6 cef
!
no ip domain-lookup
!
!
spanning-tree mode pvst
!
interface GigabitEthernet0/0/0
 ip address 10.1.1.2 255.255.255.252
 duplex auto
 speed auto
!
interface GigabitEthernet0/0/1
 ip address 10.2.2.2 255.255.255.252
 duplex auto
 speed auto
!
interface Vlan1
 no ip address
 shutdown
!
router ospf 1
 log-adjacency-changes
 network 10.1.1.0 0.0.0.3 area 0
 network 10.2.2.0 0.0.0.3 area 0
!
router rip
!
ip classless
!
ip flow-export version 9
!
no cdp run
!
line con 0
!
line aux 0
!
line vty 0 4
login
!
end

### Конфиг R3
Building configuration...

Current configuration : 1255 bytes
!
version 15.4
service timestamps log datetime msec
service timestamps debug datetime msec
service password-encryption
!
hostname www.netsec.com
!
enable secret 5 $1$mERr$WvpW0n5HghRrqnrwXCUUl.
enable password 7 08701E1D5D4C061E010803
!
aaa new-model
!
aaa authentication login local_auth local 
!
ip cef
no ipv6 cef
!
!
!
username admin password 7 082048430017151601181B00
!
no ip domain-lookup
ip domain-name www.netsec.com
!
!
spanning-tree mode pvst
!
interface GigabitEthernet0/0/0
 ip address 10.2.2.1 255.255.255.252
 duplex auto
 speed auto
!
interface GigabitEthernet0/0/1
 ip address 192.168.3.1 255.255.255.0
 duplex auto
 speed auto
!
interface Vlan1
 no ip address
 shutdown
!
router ospf 1
 log-adjacency-changes
 passive-interface GigabitEthernet0/0/1
 network 10.2.2.0 0.0.0.3 area 0
 network 192.168.3.0 0.0.0.255 area 0
!
ip classless
!
ip flow-export version 9
!
!
access-list 100 permit udp any any eq bootpc
!
no cdp run
!
banner motd ^C Unauthorized Access Prohibited ^C
!
logging trap debugging
line con 0
 transport output telnet
 exec-timeout 5 0
 login authentication local_auth
!
line aux 0
!
line vty 0 4
 login authentication local_auth
 transport input telnet
!
end
