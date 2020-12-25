# IBGP

## Цель: Настроить iBGP в офисе Москва

1. Настроить iBGP в офисе Москва между маршрутизаторами R14 и R15.

2. Настроить iBGP в провайдере Триада.

3. Настройть офис Москва так, чтобы приоритетным провайдером стал Ламас.

4. Настроить офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.

5. Все сети в лабораторной работе должны иметь IP связность.

## Решение:

1. Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 
  
1.1 Таблица стыковочных сетей.
  
1.2 Таблица выделенных IP-адресов.
  
1.3 Схема.
  
1.4 Конфигурации оборудования.

2. Настроим iBGP в офисе Москва между маршрутизаторами R14 и R15 и сделаем так, чтобы приоритетным провайдером стал Ламас. Проверим соседство.

3. Настроим iBGP в провайдере Триада. Проверим соседство.

4. Настроим офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.

5. Проверим IP связность.

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
R24|e0/0|IPv4|1.1.0.18|1.1.0.16/29|to R21
R24|e0/0|IPv6|ac10:ffff:0:5::2/64|ac10:ffff:0:5::/64|to R21
R24|e0/1|IPv4|1.1.3.9|1.1.0.8/30|to R26
R24|e0/1|IPv6|ac10:ffff:0:5035::1/64|ac10:ffff:0:5035::/64|to R26
R24|e0/2|IPv4|1.1.3.14|1.1.0.12/30|to R23
R24|e0/2|IPv6|ac10:ffff:0:5045::1/64|ac10:ffff:0:5045::/64|to R23
R24|e0/3|IPv4|1.1.0.25|1.1.0.24/29|to R18
R24|e0/3|IPv6|ac10:ffff:0:6::1/64|ac10:ffff:0:6::/64|to R18
R26|e0/0|IPv4|1.1.3.10|1.1.3.8/30|to R24
R26|e0/0|IPv6|ac10:ffff:0:5035::2/64|ac10:ffff:0:5035::/64|to R24
R26|e0/1|IPv4|1.1.0.57|1.1.0.56/29|to R28
R26|e0/1|IPv6|ac10:ffff:0:10::1/64|ac10:ffff:0:10::/64|to R28
R26|e0/2|IPv4|1.1.3.6|1.1.3.4/30|to R25
R26|e0/2|IPv6|ac10:ffff:0:5025::2/64|ac10:ffff:0:5025::/64|to R25
R26|e0/3|IPv4|1.1.0.33|1.1.0.32/29|to R18
R26|e0/3|IPv6|ac10:ffff:0:7::1/64|ac10:ffff:0:7::/64|to R18

### Часть 2: Задокументируем выделенные IP-адреса.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R14|Lo0|IPv4|172.16.0.14/32|172.16.0.0/24|Сеть управления Москва
R14|Lo0|IPv6|ac10:ffff:0:10a1::14/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R15|Lo0|IPv4|172.16.0.15/32|172.16.0.0/24|Сеть управления Москва
R15|Lo0|IPv6|ac10:ffff:0:10a1::15/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R18|Lo0|IPv4|172.16.1.18/24|172.16.1.0/24|Сеть управления С.-Петербург
R18|Lo0|IPv6|ac10:ffff:0:2a42::18/128|ac10:ffff:0:2a42::/64|Сеть управления С.-Петербург
R24|Lo0|IPv4|172.16.4.24/32|172.16.4.0/24|Сеть управления Триада
R24|Lo0|IPv6|ac10:ffff:0:50a5::24/128|ac10:ffff:0:50a5::/64|Сеть управления Триада
R26|Lo0|IPv4|172.16.4.26/32|172.16.4.0/24|Сеть управления Триада
R26|Lo0|IPv6|ac10:ffff:0:50a5::26/128|ac10:ffff:0:50a5::/64|Сеть управления Триада

#### 1.3 Схема.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab10/lab10.png)

#### 1.4 Конфигурации оборудования.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab10/Configs).

### Часть 2: Настроим iBGP в офисе Москва между маршрутизаторами R14 и R15 и сделаем так, чтобы приоритетным провайдером стал Ламас. Проверим соседство.

R14
```
router bgp 1001
 neighbor 172.16.0.15 remote-as 1001
 neighbor 172.16.0.15 update-source Loopback0
 neighbor AC10:FFFF:0:10A1::15 remote-as 1001
 neighbor AC10:FFFF:0:10A1::15 update-source Loopback0
  address-family ipv4
   neighbor 172.16.0.15 activate
   neighbor 172.16.0.15 next-hop-self
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:10A1::15 activate
  neighbor AC10:FFFF:0:10A1::15 next-hop-self
 exit
```
R15
```
router bgp 1001
 bgp default local-preference 200
 neighbor 172.16.0.14 remote-as 1001
 neighbor 172.16.0.14 update-source Loopback0
 neighbor AC10:FFFF:0:10A1::14 remote-as 1001
 neighbor AC10:FFFF:0:10A1::14 update-source Loopback0
  address-family ipv4
   neighbor 172.16.0.14 activate
   neighbor 172.16.0.14 next-hop-self
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:10A1::14 activate
  neighbor AC10:FFFF:0:10A1::14 next-hop-self
 exit
```
#### Проверим соседство.
R14 ipv4
```
R14#sh ip bgp summary
BGP router identifier 14.14.14.14, local AS number 1001
BGP table version is 26, main routing table version 26
5 network entries using 740 bytes of memory
10 path entries using 640 bytes of memory
4/2 BGP path/bestpath attribute entries using 544 bytes of memory
2 BGP AS-PATH entries using 64 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 1988 total bytes of memory
BGP activity 14/4 prefixes, 28/10 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State                                 /PfxRcd
1.1.0.1         4          101     149     151       26    0    0 01:59:24                                         2
172.16.0.15     4         1001     153     163       26    0    0 01:58:41                                         5
```
R14 ipv6
```
R14#sh bgp ipv6 unicast summary
BGP router identifier 14.14.14.14, local AS number 1001
BGP table version is 24, main routing table version 24
5 network entries using 860 bytes of memory
10 path entries using 880 bytes of memory
4/2 BGP path/bestpath attribute entries using 544 bytes of memory
2 BGP AS-PATH entries using 64 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 2348 total bytes of memory
BGP activity 14/4 prefixes, 30/10 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
AC10:FFFF:0:1::1
                4          101       9       6       24    0    0 00:00:20        2
AC10:FFFF:0:10A1::15
                4         1001     161     158       24    0    0 02:04:02        5
```
R15 ipv4
```
R15#sh ip bgp summary
BGP router identifier 15.15.15.15, local AS number 1001
BGP table version is 27, main routing table version 27
5 network entries using 740 bytes of memory
8 path entries using 512 bytes of memory
3/2 BGP path/bestpath attribute entries using 408 bytes of memory
1 BGP AS-PATH entries using 24 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 1684 total bytes of memory
BGP activity 14/4 prefixes, 30/14 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
1.1.0.5         4          301      95      93       27    0    0 01:19:24        2
172.16.0.14     4         1001     170     159       27    0    0 02:04:36        3
```
R15 ipv6
```
R15#sh bgp ipv6 unicast summary
BGP router identifier 15.15.15.15, local AS number 1001
BGP table version is 17, main routing table version 17
5 network entries using 860 bytes of memory
8 path entries using 704 bytes of memory
3/2 BGP path/bestpath attribute entries using 408 bytes of memory
1 BGP AS-PATH entries using 24 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 1996 total bytes of memory
BGP activity 14/4 prefixes, 30/14 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
AC10:FFFF:0:2::1
                4          301      95      92       17    0    0 01:19:51        2
AC10:FFFF:0:10A1::14
                4         1001     159     162       17    0    0 02:05:00        3
```
### Часть 3: Настроим iBGP в провайдере Триада. Проверим соседство.

R23
```
router bgp 520
 bgp router-id 23.23.23.23
 neighbor 172.16.4.24 remote-as 520
 neighbor 172.16.4.24 update-source Loopback0
 neighbor 172.16.4.25 remote-as 520
 neighbor 172.16.4.25 update-source Loopback0
 neighbor 172.16.4.26 remote-as 520
 neighbor 172.16.4.26 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::24 remote-as 520
 neighbor AC10:FFFF:0:50A5::24 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::25 remote-as 520
 neighbor AC10:FFFF:0:50A5::25 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::26 remote-as 520
 neighbor AC10:FFFF:0:50A5::26 update-source Loopback0
 address-family ipv4
  network 172.16.4.0 mask 255.255.255.0
  neighbor 172.16.4.24 activate
  neighbor 172.16.4.24 next-hop-self
  neighbor 172.16.4.25 activate
  neighbor 172.16.4.25 next-hop-self
  neighbor 172.16.4.26 activate
  neighbor 172.16.4.26 next-hop-self
 exit
 address-family ipv6
  network AC10:FFFF:0:50A5::/64
  neighbor AC10:FFFF:0:50A5::24 activate
  neighbor AC10:FFFF:0:50A5::24 next-hop-self
  neighbor AC10:FFFF:0:50A5::25 activate
  neighbor AC10:FFFF:0:50A5::25 next-hop-self
  neighbor AC10:FFFF:0:50A5::26 activate
  neighbor AC10:FFFF:0:50A5::26 next-hop-self
 exit
exit
```
R24
```
route-map LP-200 permit 10
 set local-preference 200
exit
router bgp 520
 bgp router-id 24.24.24.24
 neighbor 172.16.4.23 remote-as 520
 neighbor 172.16.4.23 update-source Loopback0
 neighbor 172.16.4.25 remote-as 520
 neighbor 172.16.4.25 update-source Loopback0
 neighbor 172.16.4.26 remote-as 520
 neighbor 172.16.4.26 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::23 remote-as 520
 neighbor AC10:FFFF:0:50A5::23 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::25 remote-as 520
 neighbor AC10:FFFF:0:50A5::25 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::26 remote-as 520
 neighbor AC10:FFFF:0:50A5::26 update-source Loopback0
 address-family ipv4
  network 172.16.4.0 mask 255.255.255.0
  network 192.168.0.0 route-map LP-200
  network 192.168.1.0 route-map LP-200
  neighbor 172.16.4.23 activate
  neighbor 172.16.4.23 next-hop-self
  neighbor 172.16.4.25 activate
  neighbor 172.16.4.25 next-hop-self
  neighbor 172.16.4.26 activate
  neighbor 172.16.4.26 next-hop-self
 exit
 address-family ipv6
  network AC10:FFFF:0:50A5::/64
  neighbor AC10:FFFF:0:50A5::23 activate
  neighbor AC10:FFFF:0:50A5::23 next-hop-self
  neighbor AC10:FFFF:0:50A5::25 activate
  neighbor AC10:FFFF:0:50A5::25 next-hop-self
  neighbor AC10:FFFF:0:50A5::26 activate
  neighbor AC10:FFFF:0:50A5::26 next-hop-self
 exit
exit
 ```
 R25
 ```
route-map LP-200 permit 10
 set local-preference 200
exit
ip route 172.16.2.0 255.255.255.0 1.1.0.50
ip route 172.16.3.0 255.255.255.0 1.1.0.42
ip route 192.168.4.0 255.255.255.0 1.1.0.50
ip route 192.168.5.0 255.255.255.0 1.1.0.50
ipv6 route AC10:FFFF:0:30A3::/64 AC10:FFFF:0:9::2
ipv6 route AC10:FFFF:0:30B3::/64 AC10:FFFF:0:9::2
ipv6 route AC10:FFFF:0:30C3::/64 AC10:FFFF:0:9::2
ipv6 route AC10:FFFF:0:40A4::/64 AC10:FFFF:0:8::2
router bgp 520
 bgp router-id 25.25.25.25
 neighbor 172.16.4.23 remote-as 520
 neighbor 172.16.4.23 update-source Loopback0
 neighbor 172.16.4.24 remote-as 520
 neighbor 172.16.4.24 update-source Loopback0
 neighbor 172.16.4.26 remote-as 520
 neighbor 172.16.4.26 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::23 remote-as 520
 neighbor AC10:FFFF:0:50A5::23 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::24 remote-as 520
 neighbor AC10:FFFF:0:50A5::24 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::26 remote-as 520
 neighbor AC10:FFFF:0:50A5::26 update-source Loopback0
 address-family ipv4
  network 172.16.2.0 mask 255.255.255.0
  network 172.16.3.0 mask 255.255.255.0
  network 172.16.4.0 mask 255.255.255.0
  network 192.168.4.0 route-map LP-200
  network 192.168.5.0
  neighbor 172.16.4.23 activate
  neighbor 172.16.4.23 next-hop-self
  neighbor 172.16.4.24 activate
  neighbor 172.16.4.24 next-hop-self
  neighbor 172.16.4.26 activate
  neighbor 172.16.4.26 next-hop-self
 exit
 address-family ipv6
  network AC10:FFFF:0:30A3::/64
  network AC10:FFFF:0:30B3::/64
  network AC10:FFFF:0:30C3::/64
  network AC10:FFFF:0:40A4::/64
  network AC10:FFFF:0:50A5::/64
  neighbor AC10:FFFF:0:50A5::23 activate
  neighbor AC10:FFFF:0:50A5::23 next-hop-self
  neighbor AC10:FFFF:0:50A5::24 activate
  neighbor AC10:FFFF:0:50A5::24 next-hop-self
  neighbor AC10:FFFF:0:50A5::26 activate
  neighbor AC10:FFFF:0:50A5::26 next-hop-self
 exit
exit
```
  R26
```
route-map LP-200 permit 10
 set local-preference 200
ip route 172.16.2.0 255.255.255.0 1.1.0.58
ip route 192.168.4.0 255.255.255.0 1.1.0.58
ip route 192.168.5.0 255.255.255.0 1.1.0.58
ipv6 route AC10:FFFF:0:30A3::/64 AC10:FFFF:0:10::2
ipv6 route AC10:FFFF:0:30B3::/64 AC10:FFFF:0:10::2
ipv6 route AC10:FFFF:0:30C3::/64 AC10:FFFF:0:10::2
router bgp 520
 bgp router-id 26.26.26.26
 neighbor 172.16.4.23 remote-as 520
 neighbor 172.16.4.23 update-source Loopback0
 neighbor 172.16.4.24 remote-as 520
 neighbor 172.16.4.24 update-source Loopback0
 neighbor 172.16.4.25 remote-as 520
 neighbor 172.16.4.25 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::23 remote-as 520
 neighbor AC10:FFFF:0:50A5::23 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::24 remote-as 520
 neighbor AC10:FFFF:0:50A5::24 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::25 remote-as 520
 neighbor AC10:FFFF:0:50A5::25 update-source Loopback0
 address-family ipv4
  network 172.16.2.0 mask 255.255.255.0
  network 172.16.4.0 mask 255.255.255.0
  network 192.168.4.0
  network 192.168.5.0 route-map LP-200
  neighbor 172.16.4.23 activate
  neighbor 172.16.4.23 next-hop-self
  neighbor 172.16.4.24 activate
  neighbor 172.16.4.24 next-hop-self
  neighbor 172.16.4.25 activate
  neighbor 172.16.4.25 next-hop-self
 exit
 address-family ipv6
  network AC10:FFFF:0:30A3::/64
  network AC10:FFFF:0:30B3::/64
  network AC10:FFFF:0:30C3::/64
  network AC10:FFFF:0:50A5::/64
  neighbor AC10:FFFF:0:50A5::23 activate
  neighbor AC10:FFFF:0:50A5::23 next-hop-self
  neighbor AC10:FFFF:0:50A5::24 activate
  neighbor AC10:FFFF:0:50A5::24 next-hop-self
  neighbor AC10:FFFF:0:50A5::25 activate
  neighbor AC10:FFFF:0:50A5::25 next-hop-self
 exit
exit
```
#### Проверим соседство на примере R26.

R26 ipv4
```
R26#sh ip bgp summary
BGP router identifier 26.26.26.26, local AS number 520
BGP table version is 95, main routing table version 95
9 network entries using 1296 bytes of memory
12 path entries using 960 bytes of memory
6/6 BGP path/bestpath attribute entries using 912 bytes of memory
2 BGP AS-PATH entries using 48 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 3216 total bytes of memory
BGP activity 25/7 prefixes, 66/42 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
1.1.0.34        4         2042     431     427       95    0    0 05:44:19        2
172.16.4.23     4          520      23      35       95    0    0 00:18:50        0
172.16.4.24     4          520     418     448       95    0    0 05:44:10        3
172.16.4.25     4          520      20      30       95    0    0 00:13:43        4
```
R26 ipv6
```
R26#sh bgp ipv6 unicast summary
BGP router identifier 26.26.26.26, local AS number 520
BGP table version is 74, main routing table version 74
9 network entries using 1512 bytes of memory
14 path entries using 1456 bytes of memory
5/4 BGP path/bestpath attribute entries using 760 bytes of memory
2 BGP AS-PATH entries using 48 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 3776 total bytes of memory
BGP activity 25/7 prefixes, 70/42 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
AC10:FFFF:0:7::2
                4         2042     438     421       74    0    0 05:45:58        2
AC10:FFFF:0:50A5::23
                4          520      25      39       74    0    0 00:20:28        0
AC10:FFFF:0:50A5::24
                4          520     439     458       74    0    0 05:45:50        5
AC10:FFFF:0:50A5::25
                4          520      23      34       74    0    0 00:15:21        4
```
### Часть 4: Настроим офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно. 
R18
```
router bgp 2042
 address-family ipv4
  maximum-paths 2
 exit
 address-family ipv6
  maximum-paths 2
 exit
 ```
 Введем show `ip bgp` для проверки multipath.
 ```
 R18#sh ip bgp
BGP table version is 108, local router ID is 18.18.18.18
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *m  172.16.0.0/24    1.1.0.25                               0 520 301 1001 i
 *>                   1.1.0.33                               0 520 301 1001 i
 *>  172.16.1.0/24    1.1.2.2            1024640         32768 i
 *m  172.16.2.0/24    1.1.0.25                               0 520 i
 *>                   1.1.0.33                 0             0 520 i
 *m  172.16.3.0/24    1.1.0.25                               0 520 i
 *>                   1.1.0.33                               0 520 i
 *m  192.168.0.0      1.1.0.25                               0 520 301 1001 i
 *>                   1.1.0.33                               0 520 301 1001 i
 *m  192.168.1.0      1.1.0.25                               0 520 301 1001 i
 *>                   1.1.0.33                               0 520 301 1001 i
 *>  192.168.2.0/23   1.1.2.2            1541120         32768 i
 *m  192.168.4.0      1.1.0.25                               0 520 i
 *>                   1.1.0.33                 0             0 520 i
     Network          Next Hop            Metric LocPrf Weight Path
 *m  192.168.5.0      1.1.0.25                               0 520 i
 *>                   1.1.0.33                 0             0 520 i
```
### Часть 5: Проверим IP связность.

