# PBR

## Цель:

1. Настроить политику маршрутизации в офисе Чокурдах.
  
1.1 Распределить трафик между двумя линками в сторону Триады.
  
1.2 Настроить отслеживание линков через технологию IP SLA.
  
2. Настроить для офиса Лабытнанги маршрут по-умолчанию.

## Решение:
  
1. Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 
  
1.1 Таблица стыковочных сетей.
  
1.2 Таблица выделенных IP-адресов.
  
1.3 Схема.
  
1.4 Конфигурации оборудования.
  
2. Настроим маршруты по умолчанию. 
  
2.1 Настроим для офиса Лабытнанги маршрут по-умолчанию.
  
2.2 Настроим в офисе Триада маршруты по-умолчанию для пользовательских сетей офиса Чокурдах.
  
3. Настроим политику маршрутизации в офисе Чокурдах.
  
3.1 Распределим пользовательский трафик между двумя линками в сторону Триады и настроим отслеживание линков через технологию IP SLA.
  
3.2 Проверим работоспособность.

### Часть 1: Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 

#### 1.1 Таблица стыковочных сетей.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R25|e0/1|IPv4|1.1.0.41|1.1.0.40/29|to R27
R25|e0/1|IPv6|ac10:ffff:0:8::1/64|ac10:ffff:0:8::/64|to R27
R25|e0/2|IPv4|1.1.3.5|1.1.3.4/30|to R26
R25|e0/2|IPv6|ac10:ffff:0:5025::1/64|ac10:ffff:0:5025::/64|to R26
R25|e0/3|IPv4|1.1.0.49|1.1.0.48/29|to R28
R25|e0/3|IPv6|ac10:ffff:0:9::1/64|ac10:ffff:0:9::/64|to R28
R26|e0/1|IPv4|1.1.0.57|1.1.0.56/29|to R28
R26|e0/1|IPv6|ac10:ffff:0:10::1/64|ac10:ffff:0:10::/64|to R28
R26|e0/2|IPv4|1.1.3.6|1.1.3.4/30|to R25
R26|e0/2|IPv6|ac10:ffff:0:5025::2/64|ac10:ffff:0:5025::/64|to R25
R27|e0/0|IPv4|1.1.0.42|1.1.0.40/29|to R25
R27|e0/0|IPv6|ac10:ffff:0:8::2/64|ac10:ffff:0:8::/64|to R25
R28|e0/1|IPv4|1.1.0.50|1.1.0.48/29|to R25
R28|e0/1|IPv6|ac10:ffff:0:9::2/64|ac10:ffff:0:9::/64|to R25
R28|e0/1|IPv4|1.1.0.58|1.1.0.56/29|to R26
R28|e0/1|IPv6|ac10:ffff:0:10::2/64|ac10:ffff:0:10::/64|to R26

#### 1.2 Таблица выделенных IP-адресов.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R25|Lo0|IPv4|172.16.4.25/32|172.16.4.0/24|Сеть управления Триада
R25|Lo0|IPv6|ac10:ffff:0:50a5::25/128|ac10:ffff:0:50a5::/64|Сеть управления Триада
R26|Lo0|IPv4|172.16.4.26/32|172.16.4.0/24|Сеть управления Триада
R26|Lo0|IPv6|ac10:ffff:0:50a5::26/128|ac10:ffff:0:50a5::/64|Сеть управления Триада
R27|Lo0|IPv4|172.16.3.27/32|172.16.3.0/24|Сеть управления Лабытнанги
R27|Lo0|IPv6|ac10:ffff:0:40a4::27/128|ac10:ffff:0:40a4::/64|Сеть управления Лабытнанги
R28|e0/2.30|IPv4|172.16.2.1/24|172.16.2.0/24|Сеть управления Чокурдах
R28|e0/2.30|IPv6|ac10:ffff:0:30a3::28/64|ac10:ffff:0:30a3::/64|Сеть управления Чокурдах
R28|e0/2.31|IPv4|192.168.4.1/24|192.168.4.0/24|Пользовательская сеть Чокурдах для VPC30
R28|e0/2.31|IPv6|ac10:ffff:0:30b3::28/64|ac10:ffff:0:30b3::/64|Пользовательская сеть Чокурдах для VPC30
R28|e0/2.32|IPv4|192.168.5.1/24|192.168.5.0/24|Пользовательская сеть Чокурдах для VPC31
R28|e0/2.32|IPv6|ac10:ffff:0:30c3::28/64|ac10:ffff:0:30c3::/64|Пользовательская сеть Чокурдах для VPC31
SW29|Int vlan 30|IPv4|172.16.2.29/24|172.16.2.0/24|Сеть управления Чокурдах
SW29|Int vlan 30|IPv6|ac10:ffff:0:30a3::29/64|ac10:ffff:0:30a3::/64|Сеть управления Чокурдах
VPC30|eth0|IPv4|192.168.4.2|192.168.4.0/24|Пользовательская сеть  Чокурдах для VPC30
VPC30|eth0|IPv6|autoconfig|ac10:ffff:0:30b3::/64|Пользовательская сеть  Чокурдах для VPC30
VPC31|eth0|IPv4|192.168.5.2|192.168.5.0/24|Пользовательская сеть  Чокурдах для VPC31
VPC31|eth0|IPv6|autoconfig|ac10:ffff:0:30c3::/64|Пользовательская сеть  Чокурдах для VPC31

#### 1.3 Схема.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab05/lab05.png)

#### 1.4 Конфигурации оборудования.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab05/Configs).

### Часть 2: Настроим маршруты по умолчанию.

#### 2.1 Настроим для офиса Лабытнанги маршрут по-умолчанию.
```
conf t
ip route 0.0.0.0 0.0.0.0 1.1.0.41
ipv6 route ::/0 AC10:FFFF:0:8::1
```
#### 2.2 Настроим в офисе Триада маршруты по-умолчанию для пользовательских сетей офиса Чокурдах.

На R25:
```
conf t
ip route 192.168.4.0 255.255.254.0 1.1.0.50
```
На R26:
```
conf t
ip route 192.168.4.0 255.255.254.0 1.1.0.58
```
### Часть 3: Настроим политику маршрутизации в офисе Чокурдах.
#### 3.1 Распределим пользовательский трафик между двумя линками в сторону Триады и настроим отслеживание линков через технологию IP SLA.
```
conf t
ip sla 1
 icmp-echo 1.1.0.49 source-ip 1.1.0.50
 frequency 5
exit
ip sla schedule 1 life forever start-time now
ip sla 2
 icmp-echo 1.1.0.57 source-ip 1.1.0.58
 frequency 5
exit
ip sla schedule 2 life forever start-time now
ip sla 3
 icmp-echo AC10:FFFF:0:9::1 source-ip AC10:FFFF:0:9::2
 frequency 5
exit
ip sla schedule 3 life forever start-time now
ip sla 4
 icmp-echo AC10:FFFF:0:10::1 source-ip AC10:FFFF:0:10::2
 frequency 5
 exit
ip sla schedule 4 life forever start-time now
track 1 ip sla 1 reachability
track 2 ip sla 2 reachability
track 3 ip sla 3 reachability
track 4 ip sla 4 reachability
ip access-list extended vlan31
 permit ip 192.168.4.0 0.0.0.255 any
exit
ip access-list extended vlan32
 permit ip 192.168.5.0 0.0.0.255 any
exit
ipv6 access-list vlan31_ipv6
 permit ipv6 AC10:FFFF:0:30B3::/64 any
exit
ipv6 access-list vlan32_ipv6
 permit ipv6 AC10:FFFF:0:30C3::/64 any
exit
route-map PBR permit 10
 match ip address vlan31
 set ip next-hop verify-availability 1.1.0.49 10 track 1
 set ip next-hop verify-availability 1.1.0.57 20 track 2
exit
route-map PBR permit 15
 match ipv6 address vlan31_ipv6
 set ipv6 next-hop AC10:FFFF:0:9::2 10 track 3
 set ipv6 next-hop AC10:FFFF:0:10::2 20 track 4
exit
route-map PBR permit 20
 match ip address vlan32
 set ip next-hop verify-availability 1.1.0.57 10 track 2
 set ip next-hop verify-availability 1.1.0.49 20 track 1
exit
route-map PBR permit 25
 match ipv6 address vlan32_ipv6
 set ipv6 next-hop AC10:FFFF:0:10::2 10 track 4
 set ipv6 next-hop AC10:FFFF:0:9::2 20 track 3
exit
interface Ethernet0/2.31
 ip policy route-map PBR
exit
interface Ethernet0/2.32
 ip policy route-map PBR
exit
```
#### 3.2 Проверим работоспособность.

Работоспособность IPv6 мы проверить не можем, т.к. есть ограничения в EVE-NG.

Для проверки IPv4 мы будем делать трассировку до IP-адреса интерфейса маршрутизатора Лабытнанги R27 `1.1.0.42`

VPC30
```
VPCS> trace 1.1.0.42
trace to 1.1.0.42, 8 hops max, press Ctrl+C to stop
 1   192.168.4.1   1.041 ms  0.624 ms  0.539 ms
 2   1.1.0.49   0.854 ms  0.791 ms  0.776 ms
 3   *1.1.0.42   1.689 ms (ICMP type:3, code:3, Destination port unreachable)  *
```
VPC31
```
VPCS> trace 1.1.0.42
trace to 1.1.0.42, 8 hops max, press Ctrl+C to stop
 1   192.168.5.1   1.090 ms  0.670 ms  0.678 ms
 2   1.1.0.57   1.468 ms  0.786 ms  0.724 ms
 3   *1.1.0.57   0.745 ms (ICMP type:3, code:1, Destination host unreachable)  *
```
Распределение трафика работает.

Далее проверим работу отслеживания линков через технологию IP SLA.

Для этого мы выключаем порт e0/1 на R26 и сделаем трассировки на обоих VPC:

VPC30
```
VPCS> trace 1.1.0.42
trace to 1.1.0.42, 8 hops max, press Ctrl+C to stop
 1   192.168.4.1   1.008 ms  0.544 ms  0.615 ms
 2   1.1.0.49   0.823 ms  0.824 ms  0.704 ms
 3   *1.1.0.42   1.869 ms (ICMP type:3, code:3, Destination port unreachable)  *
```
VPC31
```
VPCS> trace 1.1.0.42
trace to 1.1.0.42, 8 hops max, press Ctrl+C to stop
 1   192.168.5.1   0.713 ms  0.519 ms  0.476 ms
 2   1.1.0.49   0.883 ms  0.721 ms  0.820 ms
 3   *1.1.0.42   2.671 ms (ICMP type:3, code:3, Destination port unreachable)  *

VPCS>
```
Весь пользовательский трафик c обоих VPC пошел через порт e0/1 на R25.

Ну и включим обратно порт e0/1 на R26 и выключим порт e0/3 на R25 и проверим трассировки.

VPC30
```
VPCS> trace 1.1.0.42
trace to 1.1.0.42, 8 hops max, press Ctrl+C to stop
 1   192.168.4.1   0.954 ms  0.780 ms  0.569 ms
 2   1.1.0.57   1.026 ms  0.840 ms  0.931 ms
 3   *1.1.0.57   0.853 ms (ICMP type:3, code:1, Destination host unreachable)  *

VPCS>
```
VPC31
```
VPCS> trace 1.1.0.42
trace to 1.1.0.42, 8 hops max, press Ctrl+C to stop
 1   192.168.5.1   0.882 ms  0.600 ms  0.663 ms
 2   1.1.0.57   0.879 ms  0.554 ms  0.675 ms
 3   *1.1.0.57   0.652 ms (ICMP type:3, code:1, Destination host unreachable)  *

VPCS>
```
Весь пользовательский трафик c обоих VPC пошел через порт e0/0 на R26.

Работает!
