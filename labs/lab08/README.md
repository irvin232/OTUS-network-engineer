# EIGRP

## Цель:

1. В офисе С.-Петербург настроить EIGRP;

2. R32 получает только маршрут по-умолчанию;

3. R16-17 анонсируют только суммарные префиксы;

4. Использовать EIGRP named-mode для настройки сети.

## Решение:
  
1. Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 
  
1.1 Таблица стыковочных сетей.
  
1.2 Таблица выделенных IP-адресов.
  
1.3 Схема.
  
1.4 Конфигурации оборудования.

2. Настроим EIGRP на R18 и распространим с него маршрут по умолчанию. Хоть и заблаговременно, но проверим что он получает суммарные префиксы.

3. Настроим EIGRP на R16 и R17. Добавим анонсы суммарных префиксов. Проверим суммарные префиксы и маршрут по умолчанию.

4. Настроим EIGRP на R32 и добавим настройки, что бы он получал только маршрут по-умолчанию. Проверим что он получает только маршрут по умолчанию.

5. Настроим EIGRP на SW9 и SW10. Проверим что они получают суммарные префиксы и маршрут по умолчанию.

### Часть 1: Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 

#### 1.1 Таблица стыковочных сетей.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R18|e0/0|IPv4|1.1.2.5|1.1.2.4/30|to R16
R18|e0/0|IPv6|ac10:ffff:0:2022::1/64|ac10:ffff:0:2022::/64|to R16
R18|e0/1|IPv4|1.1.2.1|1.1.2.0/30|to R17
R18|e0/1|IPv6|ac10:ffff:0:2012::1/64|ac10:ffff:0:2012::/64|to R17
R18|e0/2|IPv4|1.1.0.26|1.1.0.24/29|to R24
R18|e0/2|IPv6|ac10:ffff:0:6::2/64|ac10:ffff:0:6::/64|to R24
R18|e0/3|IPv4|1.1.0.34|1.1.0.32/29|to R26
R18|e0/3|IPv6|ac10:ffff:0:7::2/64|ac10:ffff:0:7::/64|to R26
R17|e0/0|IPv4|1.1.2.9|1.1.2.8/30|to SW9
R17|e0/0|IPv6|ac10:ffff:0:2032::1/64|ac10:ffff:0:2032::/64|to SW9
R17|e0/1|IPv4|1.1.2.2|1.1.2.0/30|to R18
R17|e0/1|IPv6|ac10:ffff:0:2012::2/64|ac10:ffff:0:2012::/64|to R18
R17|e0/2|IPv4|1.1.2.13|1.1.2.12/30|to SW10
R17|e0/2|IPv6|ac10:ffff:0:2042::1/64|ac10:ffff:0:2042::/64|to SW10
R16|e0/0|IPv4|1.1.2.21|1.1.2.20/30|to SW10
R16|e0/0|IPv6|ac10:ffff:0:2062::1/64|ac10:ffff:0:2062::/64|to SW10
R16|e0/1|IPv4|1.1.2.6|1.1.2.4/30|to R18
R16|e0/1|IPv6|ac10:ffff:0:2022::2/64|ac10:ffff:0:2022::/64|to R18
R16|e0/2|IPv4|1.1.2.17|1.1.2.16/30|to SW9
R16|e0/2|IPv6|ac10:ffff:0:2052::1/64|ac10:ffff:0:2052::/64|to SW9
R16|e0/3|IPv4|1.1.2.25|1.1.2.24/30|to R32
R16|e0/3|IPv6|ac10:ffff:0:2072::1/64|ac10:ffff:0:2072::/64|to R32
R32|e0/0|IPv4|1.1.2.26|1.1.2.24/30|to R16
R32|e0/0|IPv6|ac10:ffff:0:2072::2/64|ac10:ffff:0:2072::/64|to R16
SW9|e0/0-1(Po1)|IPv4|1.1.2.29|1.1.2.28/30|to SW10
SW9|e0/0-1(Po1)|IPv6|ac10:ffff:0:2082::1/64|ac10:ffff:0:2082::/64|to SW10
SW9|e0/3|IPv4|1.1.2.10|1.1.2.8/30|to R17
SW9|e0/3|IPv6|ac10:ffff:0:2032::2/64|ac10:ffff:0:2032::/64|to R17
SW9|e1/0|IPv4|1.1.2.18|1.1.2.16/30|to R16
SW9|e1/0|IPv6|ac10:ffff:0:2052::2/64|ac10:ffff:0:2052::/64|to R16
SW10|e0/0-1(Po1)|IPv4|1.1.2.30|1.1.2.28/30|to SW9
SW10|e0/0-1(Po1)|IPv6|ac10:ffff:0:2082::2/64|ac10:ffff:0:2082::/64|to SW9
SW10|e0/3|IPv4|1.1.2.22|1.1.2.20/30|to R16
SW10|e0/3|IPv6|ac10:ffff:0:2062::2/64|ac10:ffff:0:2062::/64|to R16
SW10|e1/0|IPv4|1.1.2.14|1.1.2.12/30|to R17
SW10|e1/0|IPv6|ac10:ffff:0:2042::2/64|ac10:ffff:0:2042::/64|to R17

#### 1.2 Таблица выделенных IP-адресов.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R18|Lo0|IPv4|172.16.1.18/24|172.16.1.0/24|Сеть управления С.-Петербург
R18|Lo0|IPv6|ac10:ffff:0:2a42::18/128|ac10:ffff:0:2a42::/64|Сеть управления С.-Петербург
R17|Lo0|IPv4|172.16.1.17/24|172.16.1.0/24|Сеть управления С.-Петербург
R17|Lo0|IPv6|ac10:ffff:0:2a42::17/128|ac10:ffff:0:2a42::/64|Сеть управления С.-Петербург
R16|Lo0|IPv4|172.16.1.16/24|172.16.1.0/24|Сеть управления С.-Петербург
R16|Lo0|IPv6|ac10:ffff:0:2a42::16/128|ac10:ffff:0:2a42::/64|Сеть управления С.-Петербург
R32|Lo0|IPv4|172.16.1.32/24|172.16.1.0/24|Сеть управления С.-Петербург
R32|Lo0|IPv6|ac10:ffff:0:2a42::32/128|ac10:ffff:0:2a42::/64|Сеть управления С.-Петербург
SW9|Lo0|IPv4|172.16.1.9/24|172.16.1.0/24|Сеть управления С.-Петербург
SW9|Lo0|IPv6|ac10:ffff:0:2a42::9/128|ac10:ffff:0:2a42::/64|Сеть управления С.-Петербург
SW9|Int vlan 21|IPv4|192.168.2.1/24|192.168.2.0/24|Пользовательская сеть С.-Петербург для VPC8 
SW9|Int vlan 21|IPv6|ac10:ffff:0:2b42::1/64|ac10:ffff:0:2b42::/64|Пользовательская сеть С.-Петербург для VPC8 
SW10|Lo0|IPv4|172.16.1.10/24|172.16.1.0/24|Сеть управления С.-Петербург
SW10|Lo0|IPv6|ac10:ffff:0:2a42::10/128|ac10:ffff:0:2a42::/64|Сеть управления С.-Петербург
SW10|Int vlan 22|IPv4|192.168.3.1/24|192.168.3.0/24|Пользовательская сеть С.-Петербург для VPC8
SW10|Int vlan 22|IPv6|ac10:ffff:0:2с42::1/64|ac10:ffff:0:2с42::/64|Пользовательская сеть С.-Петербург для VPC
VPC8|eth0|IPv4|192.168.2.2|192.168.2.0/24|Пользовательская сеть  С.-Петербург для VPC8
VPC8|eth0|IPv6|autoconfig|ac10:ffff:0:2b42::/64|Пользовательская сеть  С.-Петербург для VPC8
VPC|eth0|IPv4|192.168.3.2|192.168.3.0/24|Пользовательская сеть  С.-Петербург для VPC
VPC|eth0|IPv6|autoconfig|ac10:ffff:0:2c42::/64|Пользовательская сеть  С.-Петербург для VPC

#### 1.3 Схема.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab08/lab08.png)

#### 1.4 Конфигурации оборудования.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab08/Configs).

### Часть 2: Настроим EIGRP на R18 и распространим с него маршрут по умолчанию. Хоть и заблаговременно, но проверим что он получает суммарные префиксы.

R18
```
router eigrp AS2042
 address-family ipv4 unicast autonomous-system 1
  eigrp router-id 18.18.18.18
  network 1.1.2.0 0.0.0.31
  network 172.16.1.0 0.0.0.255
  af-interface Ethernet0/1
   summary-address 0.0.0.0 0.0.0.0
  exit
  af-interface Ethernet0/0
   summary-address 0.0.0.0 0.0.0.0
  exit
 exit
 address-family ipv6 unicast autonomous-system 1
  eigrp router-id 18.18.18.18
  af-interface Ethernet0/1
   summary-address ::/0
  exit
  af-interface Ethernet0/0
   summary-address ::/0
  exit
 exit
exit
```
IPv4
```
R18#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 0.0.0.0 to network 0.0.0.0

D*    0.0.0.0/0 is a summary, 01:46:25, Null0
      1.0.0.0/8 is variably subnetted, 10 subnets, 5 masks
S        1.0.0.0/8 [1/0] via 1.1.0.24
C        1.1.0.24/29 is directly connected, Ethernet0/2
L        1.1.0.26/32 is directly connected, Ethernet0/2
C        1.1.0.32/29 is directly connected, Ethernet0/3
L        1.1.0.34/32 is directly connected, Ethernet0/3
D        1.1.2.0/27 [90/1536000] via 1.1.2.6, 01:53:35, Ethernet0/0
                    [90/1536000] via 1.1.2.2, 01:53:35, Ethernet0/1
C        1.1.2.0/30 is directly connected, Ethernet0/1
L        1.1.2.1/32 is directly connected, Ethernet0/1
C        1.1.2.4/30 is directly connected, Ethernet0/0
L        1.1.2.5/32 is directly connected, Ethernet0/0
      172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
D        172.16.1.0/24 [90/1024640] via 1.1.2.6, 01:48:16, Ethernet0/0
                       [90/1024640] via 1.1.2.2, 01:48:16, Ethernet0/1
C        172.16.1.18/32 is directly connected, Loopback0
D     192.168.2.0/23 [90/1541120] via 1.1.2.6, 01:52:01, Ethernet0/0
                     [90/1541120] via 1.1.2.2, 01:52:01, Ethernet0/1
```
IPv6
```
R18#sh ipv6 route
IPv6 Routing Table - default - 8 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
D   ::/0 [5/1280]
     via Null0, directly connected
D   AC10:FFFF::/48 [90/1024640]
     via FE80::16, Ethernet0/0
     via FE80::17, Ethernet0/1
C   AC10:FFFF:0:6::/64 [0/0]
     via Ethernet0/2, directly connected
L   AC10:FFFF:0:6::2/128 [0/0]
     via Ethernet0/2, receive
C   AC10:FFFF:0:7::/64 [0/0]
     via Ethernet0/3, directly connected
L   AC10:FFFF:0:7::2/128 [0/0]
     via Ethernet0/3, receive
LC  AC10:FFFF:0:2A42::18/128 [0/0]
     via Loopback0, receive
L   FF00::/8 [0/0]
     via Null0, receive
```
### Часть 3: Настроим EIGRP на R16 и R17. Добавим анонсы суммарных префиксов. Проверим суммарные префиксы и маршрут по умолчанию.

Настройки для R16 и R17 идентичны, за исключением `router-id`. Ниже пример настройки R16.
```
router eigrp AS2042
 address-family ipv4 unicast autonomous-system 1
  network 1.1.2.0 0.0.0.31
  network 172.16.1.0 0.0.0.255
  eigrp router-id 16.16.16.16
  af-interface Ethernet0/1
   summary-address 1.1.2.0 255.255.255.224
   summary-address 172.16.1.0 255.255.255.0
   summary-address 192.168.2.0 255.255.254.0
  exit
  af-interface Ethernet0/3
   summary-address 1.1.2.0 255.255.255.224
   summary-address 172.16.1.0 255.255.255.0
   summary-address 192.168.2.0 255.255.254.0
  exit
  af-interface Ethernet0/0
   summary-address 1.1.2.0 255.255.255.224
   summary-address 172.16.1.0 255.255.255.0
   summary-address 192.168.2.0 255.255.254.0
  exit
  af-interface Ethernet0/2
   summary-address 1.1.2.0 255.255.255.224
   summary-address 172.16.1.0 255.255.255.0
   summary-address 192.168.2.0 255.255.254.0
  exit
 exit
 address-family ipv6 unicast autonomous-system 1
  eigrp router-id 16.16.16.16
  af-interface Ethernet0/0
   summary-address AC10:FFFF::/48
  exit
  af-interface Ethernet0/1
   summary-address AC10:FFFF::/48
  exit
  af-interface Ethernet0/2
   summary-address AC10:FFFF::/48
  exit
  af-interface Ethernet0/3
   summary-address AC10:FFFF::/48
  exit
 exit
exit
```
Проверим суммарные префиксы и маршрут по умолчанию на примере R16.

IPv4
```
R16#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 1.1.2.5 to network 0.0.0.0

D*    0.0.0.0/0 [90/1024640] via 1.1.2.5, 01:04:49, Ethernet0/1
      1.0.0.0/8 is variably subnetted, 12 subnets, 3 masks
D        1.1.2.0/27 is a summary, 01:04:49, Null0
C        1.1.2.4/30 is directly connected, Ethernet0/1
L        1.1.2.6/32 is directly connected, Ethernet0/1
D        1.1.2.8/30 [90/1536000] via 1.1.2.18, 01:04:49, Ethernet0/2
D        1.1.2.12/30 [90/1536000] via 1.1.2.22, 01:05:09, Ethernet0/0
C        1.1.2.16/30 is directly connected, Ethernet0/2
L        1.1.2.17/32 is directly connected, Ethernet0/2
C        1.1.2.20/30 is directly connected, Ethernet0/0
L        1.1.2.21/32 is directly connected, Ethernet0/0
C        1.1.2.24/30 is directly connected, Ethernet0/3
L        1.1.2.25/32 is directly connected, Ethernet0/3
D        1.1.2.28/30 [90/1536000] via 1.1.2.22, 01:04:49, Ethernet0/0
                     [90/1536000] via 1.1.2.18, 01:04:49, Ethernet0/2
      172.16.0.0/16 is variably subnetted, 5 subnets, 2 masks
D        172.16.1.0/24 is a summary, 01:04:49, Null0
D        172.16.1.9/32 [90/1024640] via 1.1.2.18, 01:04:49, Ethernet0/2
D        172.16.1.10/32 [90/1024640] via 1.1.2.22, 01:05:09, Ethernet0/0
C        172.16.1.16/32 is directly connected, Loopback0
D        172.16.1.32/32 [90/1024640] via 1.1.2.26, 00:56:44, Ethernet0/3
D     192.168.2.0/23 is a summary, 01:04:49, Null0
D     192.168.2.0/24 [90/1029120] via 1.1.2.18, 01:04:49, Ethernet0/2
D     192.168.3.0/24 [90/1029120] via 1.1.2.22, 01:05:09, Ethernet0/0
```
IPv6
```
R16#sh ipv6 route
IPv6 Routing Table - default - 9 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
D   ::/0 [90/1024640]
     via FE80::18, Ethernet0/1
D   AC10:FFFF::/48 [5/1280]
     via Null0, directly connected
D   AC10:FFFF:0:2A42::9/128 [90/1024640]
     via FE80::9, Ethernet0/2
D   AC10:FFFF:0:2A42::10/128 [90/1024640]
     via FE80::10, Ethernet0/0
LC  AC10:FFFF:0:2A42::16/128 [0/0]
     via Loopback0, receive
D   AC10:FFFF:0:2A42::32/128 [90/1024640]
     via FE80::32, Ethernet0/3
D   AC10:FFFF:0:2B42::/64 [90/1029120]
     via FE80::9, Ethernet0/2
D   AC10:FFFF:0:2C42::/64 [90/1029120]
     via FE80::10, Ethernet0/0
L   FF00::/8 [0/0]
     via Null0, receive
```
### Часть 4: Настроим EIGRP на R32 и добавим настройки, что бы он получал только маршрут по-умолчанию. Проверим что он получает только маршрут по умолчанию.

R32
```
ip prefix-list FILTER-EIGRP seq 5 deny 1.1.2.0/27 le 32
ip prefix-list FILTER-EIGRP seq 10 deny 172.16.1.0/24 le 32
ip prefix-list FILTER-EIGRP seq 15 deny 192.168.2.0/23 le 32
ip prefix-list FILTER-EIGRP seq 20 permit 0.0.0.0/0 le 32
ipv6 prefix-list FILTER-EIGRP-IPV6 seq 5 deny AC10:FFFF::/48 le 128
ipv6 prefix-list FILTER-EIGRP-IPV6 seq 10 permit ::/0 le 128
router eigrp AS2042
 address-family ipv4 unicast autonomous-system 1
  network 1.1.2.0 0.0.0.31
  network 172.16.1.0 0.0.0.255
  eigrp router-id 32.32.32.32
  topology base
   distribute-list prefix FILTER-EIGRP in
  exit
 exit
 address-family ipv6 unicast autonomous-system 1
  eigrp router-id 32.32.32.32
  topology base
   distribute-list prefix-list FILTER-EIGRP-IPV6 in
  exit
 exit
exit
```
Проверим что он получает только маршрут по умолчанию.

IPv4
```
R32#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 1.1.2.25 to network 0.0.0.0

D*    0.0.0.0/0 [90/1536640] via 1.1.2.25, 01:00:29, Ethernet0/0
      1.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        1.1.2.24/30 is directly connected, Ethernet0/0
L        1.1.2.26/32 is directly connected, Ethernet0/0
      172.16.0.0/32 is subnetted, 1 subnets
C        172.16.1.32 is directly connected, Loopback0
```
IPv6
```
R32#sh ipv6 route
IPv6 Routing Table - default - 3 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
D   ::/0 [90/1536640]
     via FE80::16, Ethernet0/0
LC  AC10:FFFF:0:2A42::32/128 [0/0]
     via Loopback0, receive
L   FF00::/8 [0/0]
     via Null0, receive
```
### Часть 5: Настроим EIGRP на SW9 и SW10. Проверим что они получают суммарные префиксы и маршрут по умолчанию.

SW9
```
router eigrp AS2042
 address-family ipv4 unicast autonomous-system 1
  network 1.1.2.0 0.0.0.31
  network 172.16.1.0 0.0.0.255
  network 192.168.2.0
  eigrp router-id 9.9.9.9
 exit
 address-family ipv6 unicast autonomous-system 1
  eigrp router-id 9.9.9.9
 exit
exit
```
SW10
```
router eigrp AS2042
 address-family ipv4 unicast autonomous-system 1
  network 1.1.2.0 0.0.0.31
  network 172.16.1.0 0.0.0.255
  network 192.168.3.0
  eigrp router-id 10.10.10.10
 exit
 address-family ipv6 unicast autonomous-system 1
  eigrp router-id 10.10.10.10
 exit
exit
```
Проверим что они получают суммарные префиксы и маршрут по умолчанию на примере SW9.

IPv4
```
SW9#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 1.1.2.17 to network 0.0.0.0

D*    0.0.0.0/0 [90/1536640] via 1.1.2.17, 01:14:19, Ethernet1/0
                [90/1536640] via 1.1.2.9, 01:14:19, Ethernet0/3
      1.0.0.0/8 is variably subnetted, 7 subnets, 3 masks
D        1.1.2.0/27 [90/1536000] via 1.1.2.17, 01:14:19, Ethernet1/0
                    [90/1536000] via 1.1.2.9, 01:14:19, Ethernet0/3
C        1.1.2.8/30 is directly connected, Ethernet0/3
L        1.1.2.10/32 is directly connected, Ethernet0/3
C        1.1.2.16/30 is directly connected, Ethernet1/0
L        1.1.2.18/32 is directly connected, Ethernet1/0
C        1.1.2.28/30 is directly connected, Port-channel1
L        1.1.2.29/32 is directly connected, Port-channel1
      172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
D        172.16.1.0/24 [90/1024640] via 1.1.2.17, 01:14:19, Ethernet1/0
                       [90/1024640] via 1.1.2.9, 01:14:19, Ethernet0/3
C        172.16.1.9/32 is directly connected, Loopback0
D     192.168.2.0/23 [90/1541120] via 1.1.2.17, 01:14:19, Ethernet1/0
                     [90/1541120] via 1.1.2.9, 01:14:19, Ethernet0/3
      192.168.2.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.2.0/24 is directly connected, Vlan21
L        192.168.2.1/32 is directly connected, Vlan21
```
IPv6
```
SW9#sh ipv6 route
IPv6 Routing Table - default - 6 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, R - RIP, I1 - ISIS L1, I2 - ISIS L2
       IA - ISIS interarea, IS - ISIS summary, D - EIGRP, EX - EIGRP external
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       RL - RPL, O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1
       OE2 - OSPF ext 2, ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2
       a - Application
D   ::/0 [90/1536640]
     via FE80::17, Ethernet0/3
     via FE80::16, Ethernet1/0
D   AC10:FFFF::/48 [90/1024640]
     via FE80::16, Ethernet1/0
     via FE80::17, Ethernet0/3
LC  AC10:FFFF:0:2A42::9/128 [0/0]
     via Loopback0, receive
C   AC10:FFFF:0:2B42::/64 [0/0]
     via Vlan21, directly connected
L   AC10:FFFF:0:2B42::1/128 [0/0]
     via Vlan21, receive
L   FF00::/8 [0/0]
     via Null0, receive
```


