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
   no neighbor AC10:FFFF:0:10A1::15 activate
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:10A1::15 activate
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
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:10A1::14 activate
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

R24
```
router bgp 520
 neighbor 172.16.4.26 remote-as 520
 neighbor 172.16.4.26 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::26 remote-as 520
 neighbor AC10:FFFF:0:50A5::26 update-source Loopback0
  address-family ipv4
  neighbor 172.16.4.26 activate
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:50A5::26 activate
 exit
 ```
 R26
```
router bgp 520
 neighbor 172.16.4.24 remote-as 520
 neighbor 172.16.4.24 update-source Loopback0
 neighbor AC10:FFFF:0:50A5::24 remote-as 520
 neighbor AC10:FFFF:0:50A5::24 update-source Loopback0
 address-family ipv4
   no neighbor AC10:FFFF:0:50A5::24 activate
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:50A5::24 activate
 exit
```
#### Проверим соседство.

R24 ipv4
```
R24#sh ip bgp summary
BGP router identifier 24.24.24.24, local AS number 520
BGP table version is 18, main routing table version 18
5 network entries using 740 bytes of memory
7 path entries using 448 bytes of memory
5/3 BGP path/bestpath attribute entries using 680 bytes of memory
2 BGP AS-PATH entries using 48 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 1916 total bytes of memory
BGP activity 16/6 prefixes, 24/10 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
1.1.0.17        4          301     146     147       18    0    0 02:08:03        3
1.1.0.26        4         2042     148     149       18    0    0 02:08:34        2
172.16.4.26     4          520      92      91       18    0    0 01:16:47        2
```
R24 ipv6
```
R24#sh bgp ipv6 unicast summary
BGP router identifier 24.24.24.24, local AS number 520
BGP table version is 18, main routing table version 18
5 network entries using 860 bytes of memory
7 path entries using 616 bytes of memory
3/2 BGP path/bestpath attribute entries using 408 bytes of memory
2 BGP AS-PATH entries using 48 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 1932 total bytes of memory
BGP activity 16/6 prefixes, 24/10 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
AC10:FFFF:0:5::1
                4          301     148     147       18    0    0 02:08:35        3
AC10:FFFF:0:6::2
                4         2042     148     147       18    0    0 02:08:59        2
AC10:FFFF:0:50A5::26
                4          520      73      74       18    0    0 01:01:43        2
```
R26 ipv4
```
R26#sh ip bgp summary
BGP router identifier 26.26.26.26, local AS number 520
BGP table version is 34, main routing table version 34
5 network entries using 720 bytes of memory
7 path entries using 560 bytes of memory
5/3 BGP path/bestpath attribute entries using 760 bytes of memory
2 BGP AS-PATH entries using 48 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 2088 total bytes of memory
BGP activity 19/9 prefixes, 30/16 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
1.1.0.34        4         2042     149     151       34    0    0 02:09:37        2
172.16.4.24     4          520      92      94       34    0    0 01:17:52        5
```
R26 ipv6
```
R26#sh bgp ipv6 unicast summary
BGP router identifier 26.26.26.26, local AS number 520
BGP table version is 13, main routing table version 13
5 network entries using 840 bytes of memory
7 path entries using 728 bytes of memory
3/1 BGP path/bestpath attribute entries using 456 bytes of memory
2 BGP AS-PATH entries using 48 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 2072 total bytes of memory
BGP activity 19/9 prefixes, 30/16 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
AC10:FFFF:0:7::2
                4         2042     149     145       13    0    0 02:10:04        2
AC10:FFFF:0:50A5::24
                4          520      75      74       13    0    0 01:02:46        5
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


