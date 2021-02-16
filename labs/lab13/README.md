# VPN. GRE. DmVPN

## Цель: Настроить GRE между офисами Москва и С.-Петербург. Настроить DMVPN между офисами Москва и Чокурдах, Лабытнанги.

1. Настроить GRE между офисами Москва и С.-Петербург
2. Настроить DMVMN между Москва и Чокурдах, Лабытнанги
3. Все узлы в офисах в лабораторной работе должны иметь IP связность

## Решение:

1. Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 
  
1.1 Таблица стыковочных сетей.
  
1.2 Таблица выделенных IP-адресов.
  
1.3 Схема.
  
1.4 Конфигурации оборудования.

2. Настроитм GRE между офисами Москва и С.-Петербург

3. Настроим DMVMN между Москва и Чокурдах, Лабытнанги

4. Донастроим IP связность всех офисов и проверим IP связность.

### Часть 1: Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 

#### 1.1 Таблица стыковочных сетей.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R14|e0/0|IPv4|1.1.1.5|1.1.1.4/30|to R12
R14|e0/0|IPv6|ac10:ffff:0:1021::1/64|ac10:ffff:0:1021::/64|to R12
R14|e0/1|IPv4|1.1.1.9|1.1.1.8/30|to R13
R14|e0/1|IPv6|ac10:ffff:0:1031::1/64|ac10:ffff:0:1031::/64|to R13
R14|e0/2|IPv4|1.1.0.2|1.1.0.0/30|to R22
R14|e0/2|IPv6|ac10:ffff:0:1::2/64|ac10:ffff:0:1::/64|to R22
R14|e0/3|IPv4|1.1.1.1|1.1.1.0/30|to R19
R14|e0/3|IPv6|ac10:ffff:0:1011::1/64|ac10:ffff:0:1011::/64|to R19
R15|e0/0|IPv4|1.1.1.17|1.1.1.16/30|to R13
R15|e0/0|IPv6|ac10:ffff:0:1051::1/64|ac10:ffff:0:1051::/64|to R13
R15|e0/1|IPv4|1.1.1.13|1.1.1.12/30|to R12
R15|e0/1|IPv6|ac10:ffff:0:1041::1/64|ac10:ffff:0:1041::/64|to R12
R15|e0/2|IPv4|1.1.0.6|1.1.0.4/30|to R21
R15|e0/2|IPv6|ac10:ffff:0:2::2/64|ac10:ffff:0:2::/64|to R21
R15|e0/3|IPv4|1.1.1.21|1.1.1.20/30|to R20
R15|e0/3|IPv6|ac10:ffff:0:1061::1/64|ac10:ffff:0:1061::/64|to R20
R18|e0/0|IPv4|1.1.2.5|1.1.2.4/30|to R16
R18|e0/0|IPv6|ac10:ffff:0:2022::1/64|ac10:ffff:0:2022::/64|to R16
R18|e0/1|IPv4|1.1.2.1|1.1.2.0/30|to R17
R18|e0/1|IPv6|ac10:ffff:0:2012::1/64|ac10:ffff:0:2012::/64|to R17
R18|e0/2|IPv4|1.1.0.26|1.1.0.24/29|to R24
R18|e0/2|IPv6|ac10:ffff:0:6::2/64|ac10:ffff:0:6::/64|to R24
R18|e0/3|IPv4|1.1.0.34|1.1.0.32/29|to R26
R18|e0/3|IPv6|ac10:ffff:0:7::2/64|ac10:ffff:0:7::/64|to R26
R27|e0/0|IPv4|1.1.0.42|1.1.0.40/29|to R25
R27|e0/0|IPv6|ac10:ffff:0:8::2/64|ac10:ffff:0:8::/64|to R25
R28|e0/1|IPv4|1.1.0.50|1.1.0.48/29|to R25
R28|e0/1|IPv6|ac10:ffff:0:9::2/64|ac10:ffff:0:9::/64|to R25
R28|e0/1|IPv4|1.1.0.58|1.1.0.56/29|to R26
R28|e0/1|IPv6|ac10:ffff:0:10::2/64|ac10:ffff:0:10::/64|to R26

#### 1.2 Таблица выделенных IP-адресов.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R14|Lo0|IPv4|172.16.0.14/32|172.16.0.0/24|Сеть управления Москва
R14|Lo0|IPv6|ac10:ffff:0:10a1::14/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R15|Lo0|IPv4|172.16.0.15/32|172.16.0.0/24|Сеть управления Москва
R15|Lo0|IPv6|ac10:ffff:0:10a1::15/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R18|Lo0|IPv4|172.16.1.18/24|172.16.1.0/24|Сеть управления С.-Петербург
R18|Lo0|IPv6|ac10:ffff:0:2a42::18/128|ac10:ffff:0:2a42::/64|Сеть управления С.-Петербург
R27|Lo0|IPv4|172.16.3.27/32|172.16.3.0/24|Сеть управления Лабытнанги
R27|Lo0|IPv6|ac10:ffff:0:40a4::27/128|ac10:ffff:0:40a4::/64|Сеть управления Лабытнанги
R28|e0/2.30|IPv4|172.16.2.1/24|172.16.2.0/24|Сеть управления Чокурдах
R28|e0/2.30|IPv6|ac10:ffff:0:30a3::28/64|ac10:ffff:0:30a3::/64|Сеть управления Чокурдах
R28|e0/2.31|IPv4|192.168.4.1/24|192.168.4.0/24|Пользовательская сеть Чокурдах для VPC30
R28|e0/2.31|IPv6|ac10:ffff:0:30b3::28/64|ac10:ffff:0:30b3::/64|Пользовательская сеть Чокурдах для VPC30
R28|e0/2.32|IPv4|192.168.5.1/24|192.168.5.0/24|Пользовательская сеть Чокурдах для VPC31
R28|e0/2.32|IPv6|ac10:ffff:0:30c3::28/64|ac10:ffff:0:30c3::/64|Пользовательская сеть Чокурдах для VPC31

#### 1.3 Схема.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab13/lab13.png)

#### 1.4 Конфигурации оборудования.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab13/Configs).

### Часть 2: Настроитм GRE между офисами Москва и С.-Петербург

Выделим адресацию для GRE тунеллей:

Name 100 - R14-R18 - 10.100.0.0/24

Name 101 - R15-R18 - 10.101.0.0/24

R14 - Настроим GRE туннель. Отправим трафик пользовательских сетей в туннель добавив `ip route` на каждом маршрутизаторе. Добавим `redistribute static subnets` в ospf, что бы в случае проблем на одном из маршрутизаторов Москвы оставалась связанность.
```
interface tunnel 100
 tunnel mode gre ip
 ip address 10.100.0.1 255.255.255.0
 tunnel destination 172.16.1.18
 tunnel source loopback 0
 ip mtu 1400
 ip tcp adjust-mss 1360
 keepalive 3 3
 tunnel key 100
exit
ip route 192.168.2.0 255.255.254.0 10.100.0.2
router ospf 1
 redistribute static subnets
exit
```
R15 - Настроим GRE туннель. Отправим трафик пользовательских сетей в туннель добавив `ip route` на каждом маршрутизаторе. Добавим `redistribute static subnets` в ospf, что бы в случае проблем на одном из маршрутизаторов Москвы оставалась связанность.
```
interface tunnel 101
 tunnel mode gre ip
 ip address 10.101.0.1 255.255.255.0
 tunnel destination 172.16.1.18
 tunnel source loopback 0
 ip mtu 1400
 ip tcp adjust-mss 1360
 keepalive 3 3
 tunnel key 101
exit
ip route 192.168.2.0 255.255.254.0 10.101.0.2
router ospf 1
 redistribute static subnets
exit
```
R18 - Настроим два GRE туннеля до Маршрутизаторов Москвы. Отправим трафик пользовательских сетей в туннель добавив `ip route` на каждом маршрутизаторе.
```
interface tunnel 100
 tunnel mode gre ip
 ip address 10.100.0.2 255.255.255.0
 tunnel source loopback 0
 tunnel destination 172.16.0.14
 ip mtu 1400
 ip tcp adjust-mss 1360
 keepalive 3 3
 tunnel key 100
exit
interface tunnel 101
 tunnel mode gre ip
 ip address 10.101.0.2 255.255.255.0
 tunnel source loopback 0
 tunnel destination 172.16.0.15
 ip mtu 1400
 ip tcp adjust-mss 1360
 keepalive 3 3
 tunnel key 101
exit
ip route 192.168.0.0 255.255.254.0 10.100.0.1
ip route 192.168.0.0 255.255.254.0 10.101.0.1
```

Проверка связанности.

Сделаем трассировку с VPC7 Москвы до VPC С.-Петербург.

```
VPCS> trace 192.168.3.2 -P 1
trace to 192.168.3.2, 8 hops max (ICMP), press Ctrl+C to stop
 1   192.168.1.2   0.900 ms  0.617 ms  0.823 ms
 2   1.1.1.9   0.861 ms  0.854 ms  0.805 ms
 3   10.100.0.2   1.904 ms  1.496 ms  1.416 ms
 4   1.1.2.2   2.108 ms  1.562 ms  1.952 ms
 5   1.1.2.14   2.210 ms  2.098 ms  1.989 ms
 6   192.168.3.2   2.220 ms  2.356 ms  3.129 ms

```

### Часть 3: Настроим DMVMN между Москва и Чокурдах, Лабытнанги

Выделим адресацию для DMVMN тунеллей:

Name 200 - R14-R27-R28 - 10.200.0.0/24

Name 201 - R15-R27-R28 - 10.201.0.0/24

R14 - Настроим DMVMN Tunnel200. Настроим eigrp и анонсируем в нем тунельные сети, а так же пользовательские сети Москвы.
```
interface Tunnel200
 ip address 10.200.0.1 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip nhrp network-id 200
 ip nhrp map multicast dynamic
 tunnel mode gre multipoint
 tunnel source lo0
 ip nhrp holdtime 600
 ip nhrp redirect
 ip summary-address eigrp 200 192.168.0.0 255.255.248.0
 tunnel key 200
exit
router eigrp 200
 no auto-summary
 network 10.200.0.0 0.0.0.255
 network 10.201.0.0 0.0.0.255
 network 192.168.0.0 0.0.1.255
exit
```
R15 - Настроим DMVMN Tunnel201. Настроим eigrp и анонсируем в нем тунельные сети, а так же пользовательские сети Москвы.
```
interface Tunnel201
 ip address 10.201.0.1 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip nhrp network-id 201
 ip nhrp map multicast dynamic
 tunnel mode gre multipoint
 tunnel source lo0
 ip nhrp holdtime 600
 ip nhrp redirect
 ip summary-address eigrp 200 192.168.0.0 255.255.248.0
 tunnel key 201
exit
router eigrp 200
 no auto-summary
 network 10.200.0.0 0.0.0.255
 network 10.201.0.0 0.0.0.255
 network 192.168.0.0 0.0.1.255
exit
```
R27 - Настроим два туннеля DMVMN до двух маршрутизаторов Москвы. Настроим eigrp и анонсируем в нем тунельные сети.
```
interface Tunnel200
 ip address 10.200.0.2 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip nhrp network-id 200
 ip nhrp map 10.200.0.1 172.16.0.14
 ip nhrp map multicast 172.16.0.14
 tunnel mode gre multipoint
 ip nhrp holdtime 600
 ip nhrp shortcut
 ip nhrp nhs 10.200.0.1
 tunnel source lo0
 tunnel key 200
exit
interface Tunnel201
 ip address 10.201.0.2 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip nhrp network-id 201
 ip nhrp map 10.201.0.1 172.16.0.15
 ip nhrp map multicast 172.16.0.15
 tunnel mode gre multipoint
 ip nhrp holdtime 600
 ip nhrp shortcut
 ip nhrp nhs 10.201.0.1
 tunnel source lo0
 tunnel key 201
exit
router eigrp 200
 no auto-summary
 network 10.200.0.0 0.0.0.255
 network 10.201.0.0 0.0.0.255
exit
```
R28 - Для начала добавим статику на Loopback интерфейсы R14 и R15 через разные интерфейсы R28 для получения связанности и отказоустойчивости. Настроим два туннеля DMVMN до двух маршрутизаторов Москвы через разные физические интерфейсы. Настроим eigrp и анонсируем в нем тунельные сети, а так же пользовательские сети Чокурдах. Т.к. у нас настроен IP SLA, то добавим `deny` на пользовательские сети в `access-list` для `route-map` на vlan интерфейсах.
```
ip route 172.16.0.14 255.255.255.255 1.1.0.57
ip route 172.16.0.15 255.255.255.255 1.1.0.49
interface Tunnel200
 ip address 10.200.0.3 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip nhrp network-id 200
 ip nhrp map 10.200.0.1 172.16.0.14
 ip nhrp map multicast 172.16.0.14
 tunnel mode gre multipoint
 ip nhrp holdtime 600
 ip nhrp shortcut
 ip nhrp nhs 10.200.0.1
 tunnel source Ethernet0/0
 tunnel key 200
exit
interface Tunnel201
 ip address 10.201.0.3 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip nhrp network-id 201
 ip nhrp map 10.201.0.1 172.16.0.15
 ip nhrp map multicast 172.16.0.15
 tunnel mode gre multipoint
 ip nhrp holdtime 600
 ip nhrp shortcut
 ip nhrp nhs 10.201.0.1
 tunnel source Ethernet0/1
 tunnel key 201
exit
router eigrp 200
 no auto-summary
 network 10.200.0.0 0.0.0.255
 network 10.201.0.0 0.0.0.255
 network 192.168.4.0 0.0.1.255
exit
ip access-list extended vlan31
5 deny ip 192.168.4.0 0.0.0.255 192.168.0.0 0.0.3.255
exit
ip access-list extended vlan32
5 deny ip 192.168.5.0 0.0.0.255 192.168.0.0 0.0.3.255
exit
```
Проверка связанности.

Сделаем трассировку с VPC1 Москвы до VPC31 Чокурдаха.
```
VPCS> trace 192.168.5.2 -P 1
trace to 192.168.5.2, 8 hops max (ICMP), press Ctrl+C to stop
 1   192.168.0.2   1.741 ms  1.002 ms  1.176 ms
 2   1.1.1.17   1.160 ms  1.059 ms  1.027 ms
 3   10.201.0.3   2.174 ms  1.898 ms  1.853 ms
 4   192.168.5.2   2.902 ms  2.395 ms  2.367 ms
```
Проингуем c R15 тунельный IP R27.
```
R15>ping 10.201.0.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.201.0.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/5 ms
```
### Часть 4: Донастроим IP связность всех офисов и проверим IP связность.

R18 - Добавим `deny` на пользовательские сети Чокурдах во входящий `prefix-list` в BGP, что бы не получать их от BGP. И добавим `ip route` на пользовательские сети Чокурдах в сторону Москвы, что бы весь трафик до Чокурдах пошев туннели.
```
ip prefix-list LIST_IN1 seq 5 deny 192.168.4.0/24
ip prefix-list LIST_IN1 seq 10 deny 192.168.5.0/24
ip prefix-list LIST_IN1 seq 15 permit 0.0.0.0/0 le 32
router bgp 2042
address-family ipv4
neighbor 1.1.0.25 prefix-list LIST_IN1 in
neighbor 1.1.0.33 prefix-list LIST_IN1 in
exit
ip route 192.168.4.0 255.255.254.0 10.101.0.1
ip route 192.168.4.0 255.255.254.0 10.100.0.1
```

Проверка связанности.

Сделаем трассировку с VPC С.-Петербург до VPC31 Чокурдах.
```
VPCS> trace 192.168.5.2 -P 1
trace to 192.168.5.2, 8 hops max (ICMP), press Ctrl+C to stop
 1   192.168.3.1   0.897 ms  1.236 ms  0.237 ms
 2   1.1.2.21   0.667 ms  0.429 ms  0.354 ms
 3   1.1.2.5   0.758 ms  0.621 ms  0.695 ms
 4   10.101.0.1   1.578 ms  2.140 ms  1.224 ms
 5   10.201.0.3   2.453 ms  2.170 ms  2.257 ms
 6   192.168.5.2   2.691 ms  2.250 ms  2.501 ms
```
Сделаем трассировку с VPC30 Чокурдах до VPC8 С.-Петербурга.
```
VPCS> trace 192.168.2.2 -P 1
trace to 192.168.2.2, 8 hops max (ICMP), press Ctrl+C to stop
 1   192.168.4.1   0.649 ms  0.419 ms  0.535 ms
 2   10.200.0.1   1.904 ms  1.819 ms  1.325 ms
 3   10.100.0.2   2.766 ms  2.581 ms  2.049 ms
 4   1.1.2.2   2.616 ms  1.987 ms  2.331 ms
 5   1.1.2.10   2.795 ms  2.509 ms  2.496 ms
 6   192.168.2.2   5.714 ms  2.550 ms  9.315 ms
```
