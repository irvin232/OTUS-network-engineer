# IS-IS

## Цель: Настроить IS-IS офисе Триада

1. Настроите IS-IS в ISP Триада

2. R23 и R25 находятся в зоне 2222

3. R24 находится в зоне 24

4. R26 находится в зоне 26

## Решение:
  
1. Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 
  
1.1 Таблица стыковочных сетей.
  
1.2 Таблица выделенных IP-адресов.
  
1.3 Схема.
  
1.4 Конфигурации оборудования.

2. Настроим R23, R25 и назначим им зону 2222.

3. Настроим R24 и назначим ему зону 24.

4. Настроим R26 и назначим ему зону 26.

5. Проверим соседство на всех маршрутизаторах.

6. Настроим статические маршруты и перераспределение внешних маршрутов (route redistribution) в isis.

### Часть 1: Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 

#### 1.1 Таблица стыковочных сетей.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R23|e0/0|IPv4|1.1.0.14|1.1.0.12/30|to R22
R23|e0/0|IPv6|ac10:ffff:0:4::2/64|ac10:ffff:0:4::/64|to R22
R23|e0/1|IPv4|1.1.3.1|1.1.3.0/30|to R25
R23|e0/1|IPv6|ac10:ffff:0:5015::1/64|ac10:ffff:0:5015::/64|to R25
R23|e0/2|IPv4|1.1.3.13|1.1.3.12/30|to R24
R23|e0/2|IPv6|ac10:ffff:0:5045::1/64|ac10:ffff:0:5045::/64|to R24
R24|e0/0|IPv4|1.1.0.18|1.1.0.16/29|to R21
R24|e0/0|IPv6|ac10:ffff:0:5::2/64|ac10:ffff:0:5::/64|to R21
R24|e0/1|IPv4|1.1.3.9|1.1.0.8/30|to R26
R24|e0/1|IPv6|ac10:ffff:0:5035::1/64|ac10:ffff:0:5035::/64|to R26
R24|e0/2|IPv4|1.1.3.14|1.1.0.12/30|to R23
R24|e0/2|IPv6|ac10:ffff:0:5045::1/64|ac10:ffff:0:5045::/64|to R23
R24|e0/3|IPv4|1.1.0.25|1.1.0.24/29|to R18
R24|e0/3|IPv6|ac10:ffff:0:6::1/64|ac10:ffff:0:6::/64|to R18
R25|e0/0|IPv4|1.1.3.2|1.1.3.0/30|to R23
R25|e0/0|IPv6|ac10:ffff:0:5015::2/64|ac10:ffff:0:5015::/64|to R23
R25|e0/1|IPv4|1.1.0.41|1.1.0.40/29|to R27
R25|e0/1|IPv6|ac10:ffff:0:8::1/64|ac10:ffff:0:8::/64|to R27
R25|e0/2|IPv4|1.1.3.5|1.1.3.4/30|to R26
R25|e0/2|IPv6|ac10:ffff:0:5025::1/64|ac10:ffff:0:5025::/64|to R26
R25|e0/3|IPv4|1.1.0.49|1.1.0.48/29|to R28
R25|e0/3|IPv6|ac10:ffff:0:9::1/64|ac10:ffff:0:9::/64|to R28
R26|e0/0|IPv4|1.1.3.10|1.1.3.8/30|to R24
R26|e0/0|IPv6|ac10:ffff:0:5035::2/64|ac10:ffff:0:5035::/64|to R24
R26|e0/1|IPv4|1.1.0.57|1.1.0.56/29|to R28
R26|e0/1|IPv6|ac10:ffff:0:10::1/64|ac10:ffff:0:10::/64|to R28
R26|e0/2|IPv4|1.1.3.6|1.1.3.4/30|to R25
R26|e0/2|IPv6|ac10:ffff:0:5025::2/64|ac10:ffff:0:5025::/64|to R25
R26|e0/3|IPv4|1.1.0.33|1.1.0.32/29|to R18
R26|e0/3|IPv6|ac10:ffff:0:7::1/64|ac10:ffff:0:7::/64|to R18

#### 1.2 Таблица выделенных IP-адресов.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R23|Lo0|IPv4|172.16.4.23/32|172.16.4.0/24|Сеть управления Триада
R23|Lo0|IPv6|ac10:ffff:0:50a5::23/128|ac10:ffff:0:50a5::/64|Сеть управления Триада
R24|Lo0|IPv4|172.16.4.24/32|172.16.4.0/24|Сеть управления Триада
R24|Lo0|IPv6|ac10:ffff:0:50a5::24/128|ac10:ffff:0:50a5::/64|Сеть управления Триада
R25|Lo0|IPv4|172.16.4.25/32|172.16.4.0/24|Сеть управления Триада
R25|Lo0|IPv6|ac10:ffff:0:50a5::25/128|ac10:ffff:0:50a5::/64|Сеть управления Триада
R26|Lo0|IPv4|172.16.4.26/32|172.16.4.0/24|Сеть управления Триада
R26|Lo0|IPv6|ac10:ffff:0:50a5::26/128|ac10:ffff:0:50a5::/64|Сеть управления Триада

#### 1.3 Схема.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab07/lab07.png)

#### 1.4 Конфигурации оборудования.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab07/Configs).

### Часть 2: Настроим R23, R25 и назначим им зону 2222.

R23
```
router isis
 net 49.2222.0023.0023.0023.00
exit
int e0/1
ip router isis
exit
int e0/2
ip router isis
exit
```
R25
```
router isis
 net 49.2222.0025.0025.0025.00
exit
int e0/0
ip router isis
exit
int e0/2
ip router isis
exit
```
### Часть 3: Настроим R24 и назначим ему зону 24.

R24
```
router isis
 net 49.0024.0024.0024.0024.00
exit
int e0/1
ip router isis
exit
int e0/2
ip router isis
exit
```
### Часть 4: Настроим R26 и назначим ему зону 26.

R26
```
router isis
 net 49.0026.0026.0026.0026.00
exit
int e0/0
ip router isis
exit
int e0/2
ip router isis
exit
```
### Часть 5: Проверим соседство на всех маршрутизаторах.

R23 
```
R23#sh isis neighbors

System Id      Type Interface   IP Address      State Holdtime Circuit Id
R24            L2   Et0/2       1.1.3.14        UP    9        R24.02           
R25            L1   Et0/1       1.1.3.2         UP    9        R25.01           
R25            L2   Et0/1       1.1.3.2         UP    8        R25.01           
```
R24
```
R24#sh isis neighbors

System Id      Type Interface   IP Address      State Holdtime Circuit Id
R23            L2   Et0/2       1.1.3.13        UP    22       R24.02           
R26            L2   Et0/1       1.1.3.10        UP    7        R26.02           
```
R25
```
R25#sh isis neighbors

System Id      Type Interface   IP Address      State Holdtime Circuit Id
R23            L1   Et0/0       1.1.3.1         UP    26       R25.01           
R23            L2   Et0/0       1.1.3.1         UP    24       R25.01           
R26            L2   Et0/2       1.1.3.6         UP    6        R26.01           
```
R26
```
R26#sh isis neighbors

System Id      Type Interface   IP Address      State Holdtime Circuit Id
R24            L2   Et0/0       1.1.3.9         UP    20       R26.02           
R25            L2   Et0/2       1.1.3.5         UP    26       R26.01           
```
### Часть 6: Настроим статические маршруты и перераспределение внешних маршрутов (route redistribution) в isis.
R25
```
ip access-list extended out_subnet
permit ip 1.1.0.0 0.0.7.255 any
permit ip 172.16.0.0 0.0.7.255 any
permit ip 192.168.0.0 0.0.7.255 any
exit
ipv6 access-list out_subnet_ipv6
permit ipv6 ac10:ffff::/48 any
exit
route-map Permit_to_Distribute
match ip address out_subnet
match ipv6 address out_subnet_ipv6
exit
router isis
redistribute static ip route-map Permit_to_Distribute
default-information originate
exit
ip route 172.16.2.0 255.255.255.0 1.1.0.50 105
ip route 172.16.3.0 255.255.255.0 1.1.0.42 
ip route 192.168.4.0 255.255.254.0 1.1.0.50 105
ipv6 route AC10:FFFF:0:30A3::/64 AC10:FFFF:0:9::2 105
ipv6 route AC10:FFFF:0:30B3::/64 AC10:FFFF:0:9::2 105
ipv6 route AC10:FFFF:0:30C3::/64 AC10:FFFF:0:9::2 105
ipv6 route AC10:FFFF:0:40A4::/64 AC10:FFFF:0:8::2
```
R26
```
ip access-list extended out_subnet
permit ip 1.1.0.0 0.0.7.255 any
permit ip 172.16.0.0 0.0.7.255 any
permit ip 192.168.0.0 0.0.7.255 any
exit
ipv6 access-list out_subnet_ipv6
permit ipv6 ac10:ffff::/48 any
exit
route-map Permit_to_Distribute
match ip address out_subnet
match ipv6 address out_subnet_ipv6
exit
router isis
redistribute static ip route-map Permit_to_Distribute
default-information originate
exit
ip route 172.16.1.0 255.255.255.0 1.1.0.34
ip route 172.16.2.0 255.255.255.0 1.1.0.58
ip route 192.168.2.0 255.255.254.0 1.1.0.34
ip route 192.168.4.0 255.255.254.0 1.1.0.58
ipv6 route AC10:FFFF:0:30A3::/64 AC10:FFFF:0:10::2
ipv6 route AC10:FFFF:0:30B3::/64 AC10:FFFF:0:10::2
ipv6 route AC10:FFFF:0:30C3::/64 AC10:FFFF:0:10::2
ipv6 route AC10:ffff:0:2A42::/64 AC10:ffff:0:7::2
ipv6 route AC10:ffff:0:2B42::/64 AC10:ffff:0:7::2
ipv6 route AC10:ffff:0:2C42::/64 AC10:ffff:0:7::2
```
R24
```
ip access-list extended out_subnet
permit ip 1.1.0.0 0.0.7.255 any
permit ip 172.16.0.0 0.0.7.255 any
permit ip 192.168.0.0 0.0.7.255 any
exit
ipv6 access-list out_subnet_ipv6
permit ipv6 ac10:ffff::/48 any
exit
route-map Permit_to_Distribute
match ip address out_subnet
match ipv6 address out_subnet_ipv6
exit
router isis
redistribute static ip route-map Permit_to_Distribute
default-information originate
exit
ip route 172.16.0.0 255.255.255.0 1.1.0.17 105
ip route 172.16.1.0 255.255.255.0 1.1.0.26 105
ip route 172.16.5.0 255.255.255.0 1.1.0.17 105
ip route 172.16.6.0 255.255.255.0 1.1.0.17 105
ip route 192.168.0.0 255.255.254.0 1.1.0.17 105
ip route 192.168.2.0 255.255.254.0 1.1.0.26 105
ipv6 route AC10:ffff:0:2A42::/64 AC10:ffff:0:6::2 105
ipv6 route AC10:ffff:0:2B42::/64 AC10:ffff:0:6::2 105
ipv6 route AC10:ffff:0:2C42::/64 AC10:ffff:0:6::2 105
ipv6 route AC10:ffff:0:60A6::/64 AC10:ffff:0:5::1 105
ipv6 route AC10:ffff:0:70A7::/64 AC10:ffff:0:5::1 105
ipv6 route AC10:ffff:0:10A1::/64 AC10:ffff:0:5::1 105
ipv6 route AC10:ffff:0:10B1::/64 AC10:ffff:0:5::1 105
ipv6 route AC10:ffff:0:10C1::/64 AC10:ffff:0:5::1 105
```
R23
```
ip access-list extended out_subnet
permit ip 1.1.0.0 0.0.7.255 any
permit ip 172.16.0.0 0.0.7.255 any
permit ip 192.168.0.0 0.0.7.255 any
exit
ipv6 access-list out_subnet_ipv6
permit ipv6 ac10:ffff::/48 any
exit
route-map Permit_to_Distribute
match ip address out_subnet
match ipv6 address out_subnet_ipv6
exit
router isis
redistribute static ip route-map Permit_to_Distribute
default-information originate
exit
ip route 172.16.0.0 255.255.255.0 1.1.0.13
ip route 172.16.5.0 255.255.255.0 1.1.0.13
ip route 172.16.6.0 255.255.255.0 1.1.0.13
ip route 192.168.0.0 255.255.254.0 1.1.0.13
ipv6 route AC10:ffff:0:60A6::/64 AC10:ffff:0:4::1
ipv6 route AC10:ffff:0:70A7::/64 AC10:ffff:0:4::1
ipv6 route AC10:ffff:0:10A1::/64 AC10:ffff:0:4::1
ipv6 route AC10:ffff:0:10B1::/64 AC10:ffff:0:4::1
ipv6 route AC10:ffff:0:10C1::/64 AC10:ffff:0:4::1
```
