# OSPF

## Цель: Настроить OSPF офисе Москва. Разделить сеть на зоны. Настроить фильтрацию между зонами.

  1. Маршрутизаторы R14-R15 находятся в зоне 0 - backbone
  2. Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по-умолчанию
  3. Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию
  4. Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101
  5. Настройка для IPv6 повторяет логику IPv4
  6. План работы и изменения зафиксированы в документации

## Решение:

1. Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 
  
1.1 Таблица стыковочных сетей.
  
1.2 Таблица выделенных IP-адресов.
  
1.3 Схема.
  
1.4 Конфигурации оборудования.

2. Настроим R14, R15, R12 и R13 в Area 0 и пропишем маршрут по-умолчанию на R14 и R15 с приоритетом на R15.

3. Настроим R12 и R13 в Area 10 и проверим получение маршрута по-умолчанию.

4. Настроим R14 и R19 в Area 101 и проверим, что R19 получает только маршрут по умолчанию.

5. Настройка Area 102 и проверка, что R20 получает все маршруты, кроме маршрутов до сетей зоны 101.

5.1 Добавим на R15 `filter-list`, что бы блокировать маршруты сетей IPv4 зоны 101.

5.2 Добавим на R20 `distribute-list`, что бы блокировать маршруты сетей IPv6 зоны 101.

5.3 Проверим, что R20 получает все маршруты, кроме маршрутов до сетей зоны 101.

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
R19|e0/0|IPv4|1.1.1.2|1.1.1.0/30|to R14
R19|e0/0|IPv6|ac10:ffff:0:1011::2/64|ac10:ffff:0:1011::/64|to R14
R12|e0/1|IPv4|1.1.1.25|1.1.1.24/30|to R13
R12|e0/1|IPv6|ac10:ffff:0:1071::1/64|ac10:ffff:0:1071::/64|to R13
R12|e0/2|IPv4|1.1.1.6|1.1.1.4/30|to R14
R12|e0/2|IPv6|ac10:ffff:0:1021::2/64|ac10:ffff:0:1021::/64|to R14
R12|e0/3|IPv4|1.1.1.14|1.1.1.12/30|to R15
R12|e0/3|IPv6|ac10:ffff:0:1041::2/64|ac10:ffff:0:1041::/64|to R15
R13|e0/1|IPv4|1.1.1.26|1.1.1.24/30|to R12
R13|e0/1|IPv6|ac10:ffff:0:1071::2/64|ac10:ffff:0:1071::/64|to R12
R13|e0/2|IPv4|1.1.1.18|1.1.1.16/30|to R15
R13|e0/2|IPv6|ac10:ffff:0:1051::2/64|ac10:ffff:0:1051::/64|to R15
R13|e0/3|IPv4|1.1.1.10|1.1.1.8/30|to R14
R13|e0/3|IPv6|ac10:ffff:0:1031::2/64|ac10:ffff:0:1031::/64|to R14
R20|e0/0|IPv4|1.1.1.22|1.1.1.20/30|to R15
R20|e0/0|IPv6|ac10:ffff:0:1061::2/64|ac10:ffff:0:1061::/64|to R15

#### 1.2 Таблица выделенных IP-адресов.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R14|Lo0|IPv4|172.16.0.14/32|172.16.0.0/24|Сеть управления Москва
R14|Lo0|IPv6|ac10:ffff:0:10a1::14/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R15|Lo0|IPv4|172.16.0.15/32|172.16.0.0/24|Сеть управления Москва
R15|Lo0|IPv6|ac10:ffff:0:10a1::15/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R19|Lo0|IPv4|172.16.0.19/32|172.16.0.0/24|Сеть управления Москва
R19|Lo0|IPv6|ac10:ffff:0:10a1::19/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R20|Lo0|IPv4|172.16.0.20/32|172.16.0.0/24|Сеть управления Москва
R20|Lo0|IPv6|ac10:ffff:0:10a1::20/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R12|e0/0.10|IPv4|172.16.0.12/24|172.16.0.0/24|Сеть управления Москва
R12|e0/0.10 (HSRP)|IPv4|172.16.0.1/24|172.16.0.0/24|Сеть управления Москва
R12|e0/0.10|IPv6|ac10:ffff:0:10a1::12/64|ac10:ffff:0:10a1::/64|Сеть управления Москва
R12|e0/0.10 (HSRP)|IPv6|autoconfig|ac10:ffff:0:10a1::/64|Сеть управления Москва
R12|e0/0.11|IPv4|192.168.0.3/24|192.168.0.0/24|Пользовательская сеть  Москва для VPC1
R12|e0/0.11 (HSRP)|IPv4|192.168.0.1/24|192.168.0.0/24|Пользовательская сеть  Москва для VPC1
R12|e0/0.11|IPv6|ac10:ffff:0:10b1::12/64|ac10:ffff:0:10b1::/64|Пользовательская сеть  Москва для VPC1
R12|e0/0.11 (HSRP)|IPv6|autoconfig|ac10:ffff:0:10b1::/64|Пользовательская сеть  Москва для VPC1
R12|e0/0.12|IPv4|192.168.1.3/24|192.168.1.0/24|Пользовательская сеть  Москва для VPC7
R12|e0/0.12 (HSRP)|IPv4|192.168.1.1/24|192.168.1.0/24|Пользовательская сеть  Москва для VPC7
R12|e0/0.12|IPv6|ac10:ffff:0:10c1::12/64|ac10:ffff:0:10c1::/64|Пользовательская сеть  Москва для VPC7
R12|e0/0.12 (HSRP)|IPv6|autoconfig|ac10:ffff:0:10c1::/64|Пользовательская сеть  Москва для VPC7
R13|e0/0.10|IPv4|172.16.0.13/24|172.16.0.0/24|Сеть управления Москва
R13|e0/0.10 (HSRP)|IPv4|172.16.0.1/24|172.16.0.0/24|Сеть управления Москва
R13|e0/0.10|IPv6|ac10:ffff:0:10a1::13/64|ac10:ffff:0:10a1::/64|Сеть управления Москва
R13|e0/0.10 (HSRP)|IPv6|autoconfig|ac10:ffff:0:10a1::/64|Сеть управления Москва
R13|e0/0.11|IPv4|192.168.0.2/24|192.168.0.0/24|Пользовательская сеть  Москва для VPC1
R13|e0/0.11 (HSRP)|IPv4|192.168.0.1/24|192.168.0.0/24|Пользовательская сеть  Москва для VPC1
R13|e0/0.11|IPv6|ac10:ffff:0:10b1::13/64|ac10:ffff:0:10b1::/64|Пользовательская сеть  Москва для VPC1
R13|e0/0.11 (HSRP)|IPv6|autoconfig|ac10:ffff:0:10b1::/64|Пользовательская сеть  Москва для VPC1
R13|e0/0.12|IPv4|192.168.1.2/24|192.168.1.0/24|Пользовательская сеть  Москва для VPC7
R13|e0/0.12 (HSRP)|IPv4|192.168.1.1/24|192.168.1.0/24|Пользовательская сеть  Москва для VPC7
R13|e0/0.12|IPv6|ac10:ffff:0:10c1::13/64|ac10:ffff:0:10c1::/64|Пользовательская сеть  Москва для VPC7
R13|e0/0.12 (HSRP)|IPv6|autoconfig|ac10:ffff:0:10c1::/64|Пользовательская сеть  Москва для VPC7

#### 1.3 Схема.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab06/lab06.png)

#### 1.4 Конфигурации оборудования.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab06/Configs).

### Часть 2: Настроим R14, R15, R12 и R13 в Area 0 и пропишем маршрут по-умолчанию на R14 и R15 с приоритетом на R15.

R15
```
router ospf 1
 router-id 15.15.15.15
 default-information originate
exit
ipv6 router ospf 1
 router-id 15.15.15.15
 default-information originate
ip route 0.0.0.0 0.0.0.0 1.1.0.5 105
ipv6 route ::/0 AC10:FFFF:0:2::1 105
interface Ethernet0/0
 ip ospf 1 area 0
 ipv6 ospf 1 area 0
exit
interface Ethernet0/1
 ip ospf 1 area 0
 ipv6 ospf 1 area 0
exit
interface Ethernet0/2
 ip ospf 1 area 0
 ipv6 ospf 1 area 0
exit
```
R14
```
router ospf 1
 router-id 14.14.14.14
 default-information originate
exit
ipv6 router ospf 1
 router-id 14.14.14.14
 default-information originate
ip route 0.0.0.0 0.0.0.0 1.1.0.5
ipv6 route ::/0 AC10:FFFF:0:2::1
interface Ethernet0/0
 ip ospf 1 area 0
 ipv6 ospf 1 area 0
exit
interface Ethernet0/1
 ip ospf 1 area 0
 ipv6 ospf 1 area 0
exit
interface Ethernet0/2
 ip ospf 1 area 0
 ipv6 ospf 1 area 0
exit

```
R12
```
router ospf 1
 router-id 12.12.12.12
exit
ipv6 router ospf 1
 router-id 12.12.12.12
exit
interface Ethernet0/2
 ip ospf 1 area 0
 ipv6 ospf 1 area 0
 exit
 interface Ethernet0/3
 ip ospf 1 area 0
 ipv6 ospf 1 area 0
 exit
 ```
 R13
 ```
router ospf 1
 router-id 13.13.13.13
exit
ipv6 router ospf 1
 router-id 13.13.13.13
exit
interface Ethernet0/2
 ip ospf 1 area 0
 ipv6 ospf 1 area 0
 exit
 interface Ethernet0/3
 ip ospf 1 area 0
 ipv6 ospf 1 area 0
 exit
 ```
### Часть 3: Настроим R12 и R13 в Area 10 и проверим получение маршрута по-умолчанию.

R12
```
 interface Ethernet0/1
  ip ospf 1 area 10
  ipv6 ospf 1 area 10
 exit
 interface Ethernet0/0.10
   ip ospf 1 area 10
   ipv6 ospf 1 area 10
  exit
   interface Ethernet0/0.11
   ip ospf 1 area 10
   ipv6 ospf 1 area 10
  exit
   interface Ethernet0/0.12
   ip ospf 1 area 10
   ipv6 ospf 1 area 10
  exit
 ```
 R13
```
 interface Ethernet0/1
  ip ospf 1 area 10
  ipv6 ospf 1 area 10
 exit
 interface Ethernet0/0.10
   ip ospf 1 area 10
   ipv6 ospf 1 area 10
  exit
   interface Ethernet0/0.11
   ip ospf 1 area 10
   ipv6 ospf 1 area 10
  exit
   interface Ethernet0/0.12
   ip ospf 1 area 10
   ipv6 ospf 1 area 10
  exit
 ```
 Проверим получение маршрутов, плюс маршрута по-умолчанию на примере R12.
 IPv4
 ```
 R12#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 1.1.1.13 to network 0.0.0.0

O*E2  0.0.0.0/0 [110/10] via 1.1.1.13, 02:05:28, Ethernet0/3
                [110/10] via 1.1.1.5, 02:38:18, Ethernet0/2
      1.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
O        1.1.0.0/30 [110/20] via 1.1.1.5, 02:38:18, Ethernet0/2
O        1.1.0.4/30 [110/20] via 1.1.1.13, 02:05:28, Ethernet0/3
O IA     1.1.1.0/30 [110/20] via 1.1.1.5, 02:38:18, Ethernet0/2
C        1.1.1.4/30 is directly connected, Ethernet0/2
L        1.1.1.6/32 is directly connected, Ethernet0/2
O        1.1.1.8/30 [110/20] via 1.1.1.5, 02:38:18, Ethernet0/2
C        1.1.1.12/30 is directly connected, Ethernet0/3
L        1.1.1.14/32 is directly connected, Ethernet0/3
O        1.1.1.16/30 [110/20] via 1.1.1.13, 02:05:28, Ethernet0/3
O IA     1.1.1.20/30 [110/20] via 1.1.1.13, 02:05:28, Ethernet0/3
C        1.1.1.24/30 is directly connected, Ethernet0/1
L        1.1.1.25/32 is directly connected, Ethernet0/1
      172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.16.0.0/24 is directly connected, Ethernet0/0.10
L        172.16.0.12/32 is directly connected, Ethernet0/0.10
      192.168.0.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.0.0/24 is directly connected, Ethernet0/0.11
L        192.168.0.3/32 is directly connected, Ethernet0/0.11
      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.1.0/24 is directly connected, Ethernet0/0.12
L        192.168.1.3/32 is directly connected, Ethernet0/0.12
```
IPv6
```
R12#sh ipv6 route
IPv6 Routing Table - default - 19 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
OE2 ::/0 [110/1], tag 1
     via FE80::15, Ethernet0/3
O   AC10:FFFF:0:2::/64 [110/20]
     via FE80::15, Ethernet0/3
OI  AC10:FFFF:0:1011::/64 [110/20]
     via FE80::14, Ethernet0/2
C   AC10:FFFF:0:1021::/64 [0/0]
     via Ethernet0/2, directly connected
L   AC10:FFFF:0:1021::2/128 [0/0]
     via Ethernet0/2, receive
O   AC10:FFFF:0:1031::/64 [110/20]
     via FE80::14, Ethernet0/2
C   AC10:FFFF:0:1041::/64 [0/0]
     via Ethernet0/3, directly connected
L   AC10:FFFF:0:1041::2/128 [0/0]
     via Ethernet0/3, receive
O   AC10:FFFF:0:1051::/64 [110/20]
     via FE80::15, Ethernet0/3
OI  AC10:FFFF:0:1061::/64 [110/20]
     via FE80::15, Ethernet0/3
C   AC10:FFFF:0:1071::/64 [0/0]
     via Ethernet0/1, directly connected
L   AC10:FFFF:0:1071::1/128 [0/0]
     via Ethernet0/1, receive
C   AC10:FFFF:0:10A1::/64 [0/0]
     via Ethernet0/0.10, directly connected
L   AC10:FFFF:0:10A1::12/128 [0/0]
     via Ethernet0/0.10, receive
C   AC10:FFFF:0:10B1::/64 [0/0]
     via Ethernet0/0.11, directly connected
L   AC10:FFFF:0:10B1::12/128 [0/0]
     via Ethernet0/0.11, receive
C   AC10:FFFF:0:10C1::/64 [0/0]
     via Ethernet0/0.12, directly connected
L   AC10:FFFF:0:10C1::12/128 [0/0]
     via Ethernet0/0.12, receive
L   FF00::/8 [0/0]
     via Null0, receive
```
### Часть 4: Настроим R14 и R19 в Area 101 и проверим, что R19 получает только маршрут по умолчанию.

R14
```
interface Ethernet0/3
 ip ospf 1 area 101
 ipv6 ospf 1 area 101
exit
router ospf 1
area 101 stub no-summary
exit
ipv6 router ospf 1
area 101 stub no-summary
```
R19
```
router ospf 1
 router-id 19.19.19.19
 area 101 stub no-summary
 exit
 ipv6 router ospf 1
 router-id 19.19.19.19
 area 101 stub no-summary
exit
interface Ethernet0/0
 ip ospf 1 area 101
 ipv6 ospf 1 area 101
exit
```
Проверим, что R19 получает только маршрут по умолчанию.

IPv4
```
R19#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 1.1.1.1 to network 0.0.0.0

O*IA  0.0.0.0/0 [110/11] via 1.1.1.1, 02:42:08, Ethernet0/0
      1.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        1.1.1.0/30 is directly connected, Ethernet0/0
L        1.1.1.2/32 is directly connected, Ethernet0/0
      172.16.0.0/32 is subnetted, 1 subnets
C        172.16.0.19 is directly connected, Loopback0
```
IPv6
```
R19# sh ipv6 route
IPv6 Routing Table - default - 5 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
OI  ::/0 [110/11]
     via FE80::14, Ethernet0/0
C   AC10:FFFF:0:1011::/64 [0/0]
     via Ethernet0/0, directly connected
L   AC10:FFFF:0:1011::2/128 [0/0]
     via Ethernet0/0, receive
LC  AC10:FFFF:0:10A1::19/128 [0/0]
     via Loopback0, receive
L   FF00::/8 [0/0]
     via Null0, receive
```
### Часть 5: Настройка Area 102 и проверка, что R20 получает все маршруты, кроме маршрутов до сетей зоны 101.

Блокирование маршрутов сетей зоны 101 будем делать двумя разными способами, т.к. `filter-list` для IPv6 в EVE-NG походу не доступен. Ну и плюс опыт применения двух разных методов для блокировки маршрутов.

5.1: Добавим на R15 `filter-list`, что бы блокировать маршруты сетей IPv4 зоны 101.
```
ip prefix-list Deny-area101 seq 5 deny 1.1.1.0/30
ip prefix-list Deny-area101 seq 10 permit 0.0.0.0/0 le 32
router ospf 1
 area 102 filter-list prefix Deny-area101 in
exit
```
5.2: Добавим на R20 `distribute-list` , что бы блокировать маршруты сетей IPv6 зоны 101.
```
ipv6 prefix-list Deny-area101-ipv6 seq 5 deny AC10:FFFF:0:1011::/64
ipv6 prefix-list Deny-area101-ipv6 seq 10 permit ::/0 le 128
ipv6 router ospf 1
 distribute-list prefix-list Deny-area101-ipv6 in
exit
```
5.3: Проверим, что R20 получает все маршруты, кроме маршрутов до сетей зоны 101.

IPv4
```
R20#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 1.1.1.21 to network 0.0.0.0

O*E2  0.0.0.0/0 [110/10] via 1.1.1.21, 22:49:52, Ethernet0/0
      1.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
O IA     1.1.0.0/30 [110/40] via 1.1.1.21, 22:49:57, Ethernet0/0
O IA     1.1.0.4/30 [110/20] via 1.1.1.21, 22:49:57, Ethernet0/0
O IA     1.1.1.4/30 [110/30] via 1.1.1.21, 22:49:57, Ethernet0/0
O IA     1.1.1.8/30 [110/30] via 1.1.1.21, 22:49:57, Ethernet0/0
O IA     1.1.1.12/30 [110/20] via 1.1.1.21, 22:49:57, Ethernet0/0
O IA     1.1.1.16/30 [110/20] via 1.1.1.21, 22:49:57, Ethernet0/0
C        1.1.1.20/30 is directly connected, Ethernet0/0
L        1.1.1.22/32 is directly connected, Ethernet0/0
O IA     1.1.1.24/30 [110/30] via 1.1.1.21, 22:49:57, Ethernet0/0
      172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
O IA     172.16.0.0/24 [110/30] via 1.1.1.21, 22:49:57, Ethernet0/0
C        172.16.0.20/32 is directly connected, Loopback0
O IA  192.168.0.0/24 [110/30] via 1.1.1.21, 22:49:57, Ethernet0/0
O IA  192.168.1.0/24 [110/30] via 1.1.1.21, 22:49:57, Ethernet0/0
```
IPv6
```
R20#sh ipv6 route
IPv6 Routing Table - default - 14 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
OE2 ::/0 [110/1], tag 1
     via FE80::15, Ethernet0/0
OI  AC10:FFFF:0:2::/64 [110/20]
     via FE80::15, Ethernet0/0
OI  AC10:FFFF:0:1021::/64 [110/30]
     via FE80::15, Ethernet0/0
OI  AC10:FFFF:0:1031::/64 [110/30]
     via FE80::15, Ethernet0/0
OI  AC10:FFFF:0:1041::/64 [110/20]
     via FE80::15, Ethernet0/0
OI  AC10:FFFF:0:1051::/64 [110/20]
     via FE80::15, Ethernet0/0
C   AC10:FFFF:0:1061::/64 [0/0]
     via Ethernet0/0, directly connected
L   AC10:FFFF:0:1061::2/128 [0/0]
     via Ethernet0/0, receive
OI  AC10:FFFF:0:1071::/64 [110/30]
     via FE80::15, Ethernet0/0
OI  AC10:FFFF:0:10A1::/64 [110/30]
     via FE80::15, Ethernet0/0
LC  AC10:FFFF:0:10A1::20/128 [0/0]
     via Loopback0, receive
OI  AC10:FFFF:0:10B1::/64 [110/30]
     via FE80::15, Ethernet0/0
OI  AC10:FFFF:0:10C1::/64 [110/30]
     via FE80::15, Ethernet0/0
L   FF00::/8 [0/0]
     via Null0, receive
```
