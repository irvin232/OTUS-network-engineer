# Проектная работа

# Тема: Подключение удаленной площадки через L3 VPN с использованием маршрутизации.

## Цель.

1) На магистральной сети провайдера запустить L3VPN и организовать взаимодействие двух отдалённых узлов.

2) На стороне удаленной площадки VPC должен получать IP с сервера DHCP головного офиса.

## Используемые технологии.

- Для обеспечения базовой IP-связности внутри магистральной сети провайдера используется протокол ISIS.

- Для связи с клиентом в филиале используется протокол OSPF.

- Для передачи маршрутов одного филиала другому через магистральную сеть используется технология MPLS.

- В головном офисе DHCP поднят средствами маршрутизатора cisco. А на удаленной площадке написан DHCP-Relay.

## Решение.

#### Распределим сети:

192.168.0.0/16 - сети клиента.

172.16.0.0/24 - пользовательская сеть удаленного офиса.

10.0.0.0/16 - сети провайдера.

#### Схема:

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab15-project%20work/lab15.jpg)

#### Конфигурации оборудования:

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab15-project%20work/Configs).

#### Расшифруем понятия:

LSR - Label Switch Router - это любой маршрутизатор в сети MPLS. Называется он так, потому что выполняет какие-то операции с метками. В нашем примере это узлы: R1, R2, R3.

Customer edge (CE) - маршрутизатор на котором не включен MPLS, соответственно он не отправляет пакетов с метками, но этот маршрутизатор непосредственно присоединен к LSR (PE) маршрутизатору в MPLS VPN.

Provider edge (PE) - LSR, который непосредственно присоединен как минимум к одному CE маршрутизатору. Этот маршрутизатор является границей MPLS VPN. На нём настроены VRF и IBGP.

Provider (P) - LSR, который не присоединен непосредственно к CE маршрутизатору, что позволяет маршрутизатору отправлять пакеты исключительно на основании меток и игнорировать пользовательские маршруты.

Route Distinguishers (RD) - указывает от какого клиента выучен маршрут.

Route Targets (RT) - для определения в какую VRF PE-маршрутизатор поместит iBGP-маршрут.

#### Далее прейдем к настройке.

G1 - Поднимаем OSPF как внутри клиента, так и в сторону провайдера. Интерфейс Loopback 0 - эмулирует сеть клиента. Так же поднимаем сервер DHCP.
```
hostname G1
interface Loopback0
 ip address 192.168.255.1 255.255.255.255
interface Ethernet0/0
 description To R1
 ip address 192.168.0.2 255.255.255.0
 no shutdown
router ospf 1
 network 192.168.0.0 0.0.255.255 area 0
ip dhcp excluded-address 172.16.0.1 172.16.0.5
ip dhcp pool DHCP_172.16.0.0/24
network 172.16.0.0 255.255.255.0
default-router 172.16.0.1
domain-name otus.ru
lease 7 12 30
```
R1 - Комментарии ниже.
```
hostname R1
mpls ip
# Создается VRF и указывается для него RD и RT.
ip vrf G1
 rd 64500:100
 route-target export 64500:100
 route-target import 64500:100
interface Loopback0
 ip address 1.1.1.1 255.255.255.255
 ip router isis 1
# Клиентский интерфейс привязывается к VRF.
interface Ethernet0/0
 description To G1
 ip vrf forwarding G1
 ip address 192.168.0.1 255.255.255.0
 no shutdown
# На интерфейсе в сторону провайдерской сети должен быть включен MPLS.
interface Ethernet0/1
 description To R2
 ip address 10.0.12.1 255.255.255.0
 ip router isis 1
 mpls ip
 no shutdown
# OSPF для связи с G1 запускается в конкретном VRF. В него импортируются маршруты из BGP.
router ospf 2 vrf G1
 redistribute bgp 64500 subnets
 network 192.168.0.0 0.0.255.255 area 0
# ISIS для внутренней связности в сети провайдера.
router isis 1
 net 10.0000.0000.0001.00
# Удалённый PE должен быть настроен в качестве соседа. Поскольку это IBGP, указываем, что пакеты отправляются от интерфейса Loopback0.
router bgp 64500
 neighbor 3.3.3.3 remote-as 64500
 neighbor 3.3.3.3 update-source Loopback0
# Это то, что превращает BGP в MBGP - address family vpnv4 для передачии клиентских маршрутов.
 address-family vpnv4
 neighbor 3.3.3.3 activate
 neighbor 3.3.3.3 send-community both
# Этот address-family нужен для того, чтобы в BGP импортировать клиентские маршруты. В данном случае из OSPF. Оперирует в VRF G1
 address-family ipv4 vrf G1
 redistribute ospf 2 vrf G1
# Настроим LSR ID вручную.
mpls ldp router-id Loopback0 force
```
R2 - Ввыступает в роли P. Поэтому у него нет никаких забот, кроме MPLS на интерфейсах и ISIS для внутренней связности в сети провайдера.
```
hostname R2
mpls ip
interface Loopback0
 ip address 2.2.2.2 255.255.255.255
 ip router isis 1
interface Ethernet0/1
 description To R1
 ip address 10.0.12.2 255.255.255.0
 ip router isis 1
 mpls ip
 no shutdown
interface Ethernet0/0
 description To R3
 ip address 10.0.23.2 255.255.255.0
 ip router isis 1
 mpls ip
 no shutdown
router isis 1
 net 10.0000.0000.0002.00
mpls ldp router-id Loopback0 force
```
R3 - Повторяет настройку R1.
```
hostname R3
mpls ip
ip vrf G1
 rd 64500:100
 route-target export 64500:100
 route-target import 64500:100
interface Loopback0
 ip address 3.3.3.3 255.255.255.255
 ip router isis 1
interface Ethernet0/1
 description To R2
 ip address 10.0.23.3 255.255.255.0
 ip router isis 1
 mpls ip
 no shutdown
interface Ethernet0/0
 description To F1
 ip vrf forwarding G1
 ip address 192.168.1.1 255.255.255.0
 no shutdown
router ospf 2 vrf G1
 redistribute bgp 64500 subnets
 network 192.168.0.0 0.0.255.255 area 0
router isis 1
 net 10.0000.0000.0003.00
router bgp 64500
 neighbor 1.1.1.1 remote-as 64500
 neighbor 1.1.1.1 update-source Loopback0
 address-family vpnv4
 neighbor 1.1.1.1 activate
 neighbor 1.1.1.1 send-community both
 address-family ipv4 vrf G1
 redistribute ospf 2 vrf G1
mpls ldp router-id Loopback0
```
F1 - Повторяет настройки G1. Добаядется анонс в OSPF пользовательской сети.
```
hostname F1
interface Loopback0
 ip address 192.168.255.2 255.255.255.255
interface Ethernet0/0
 description To R3
 ip address 192.168.1.2 255.255.255.0
 no shutdown
interface Ethernet0/1
 description To S1
 ip address 192.168.2.1 255.255.255.0
router ospf 1
 network 172.16.0.0 0.0.0.255 area 0
 network 192.168.0.0 0.0.255.255 area 0
```
S1 - Коммутатор уровня доступа. Поднят OSPF для IP связанности. А так же на нем терминируется пользовательская сеть удаленного доступа, на которой написан DHCP Relay на DHCP сервер главного офиса.
```
hostname S1
vlan 10
name User_lan
interface Ethernet0/0
 description To F1
 no switchport
 ip address 192.168.2.2 255.255.255.0
interface Ethernet0/1
 description To VPC
 switchport access vlan 10
 switchport mode access
ip dhcp snooping vlan 10
no ip dhcp snooping information option
ip dhcp snooping
router ospf 1
 network 172.16.0.0 0.0.0.255 area 0
 network 192.168.0.0 0.0.255.255 area 0
 ```

Проверка IP связанности. Проверяем с VPC.

Нет IP
```
VPCS> sh ip

NAME        : VPCS[1]
IP/MASK     : 0.0.0.0/0
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:07
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500
```
Есть IP
```
VPCS> dhcp
DDORA IP 172.16.0.6/24 GW 172.16.0.1

VPCS> sh ip

NAME        : VPCS[1]
IP/MASK     : 172.16.0.6/24
GATEWAY     : 172.16.0.1
DNS         :
DHCP SERVER : 192.168.0.2
DHCP LEASE  : 649797, 649800/324900/568575
DOMAIN NAME : otus.ru
MAC         : 00:50:79:66:68:07
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500
```
Пропингуем главный офис. Связь есть.
```
VPCS> ping 192.168.255.1 -P 1

84 bytes from 192.168.255.1 icmp_seq=1 ttl=250 time=1.744 ms
84 bytes from 192.168.255.1 icmp_seq=2 ttl=250 time=2.160 ms
84 bytes from 192.168.255.1 icmp_seq=3 ttl=250 time=1.267 ms
84 bytes from 192.168.255.1 icmp_seq=4 ttl=250 time=1.292 ms
84 bytes from 192.168.255.1 icmp_seq=5 ttl=250 time=1.555 ms
```
Ну и трассировка для информации.
```
VPCS> trace 192.168.255.1 -P 1
trace to 192.168.255.1, 8 hops max (ICMP), press Ctrl+C to stop
 1   172.16.0.1   5.324 ms  0.248 ms  0.165 ms
 2   192.168.2.1   0.548 ms  0.399 ms  0.388 ms
 3   192.168.1.1   0.837 ms  0.611 ms  0.533 ms
 4   10.0.23.2   1.527 ms  0.983 ms  0.776 ms
 5   192.168.0.1   0.767 ms  0.669 ms  0.689 ms
 6   192.168.255.1   1.102 ms  0.879 ms  0.874 ms
```

Работает! 
