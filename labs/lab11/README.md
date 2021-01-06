# BGP. Фильтрация

## Цель:

1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).

2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).

3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по-умолчанию.

4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по-умолчанию и префикс офиса С.-Петербург.

5. Все сети в лабораторной работе должны иметь IP связность.

## Решение:

1. Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 
  
1.1 Таблица стыковочных сетей.
  
1.2 Таблица выделенных IP-адресов.
  
1.3 Схема.
  
1.4 Конфигурации оборудования.

2. Настроим фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).

3. Настроим фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).

4. Настроим провайдер Киторн так, чтобы в офис Москва отдавался только маршрут по-умолчанию.

5. Настроим провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по-умолчанию и префикс офиса С.-Петербург.

6. Проверим IP связность.

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
R21|e0/0|IPv4|1.1.0.5|1.1.0.4/30|to R15
R21|e0/0|IPv6|ac10:ffff:0:2::1/64|ac10:ffff:0:2::/64|to R15
R21|e0/1|IPv4|1.1.0.10|1.1.0.8/30|to R22
R21|e0/1|IPv6|ac10:ffff:0:3::2/64|ac10:ffff:0:3::/64|to R22
R21|e0/2|IPv4|1.1.0.17|1.1.0.16/29|to R24
R21|e0/2|IPv6|ac10:ffff:0:5::1/64|ac10:ffff:0:5::/64|to R24
R22|e0/0|IPv4|1.1.0.1|1.1.0.0/30|to R14
R22|e0/0|IPv6|ac10:ffff:0:1::1/64|ac10:ffff:0:1::/64|to R14
R22|e0/1|IPv4|1.1.0.9|1.1.0.8/30|to R21
R22|e0/1|IPv6|ac10:ffff:0:3::1/64|ac10:ffff:0:3::/64|to R21
R22|e0/2|IPv4|1.1.0.13|1.1.0.12/30|to R23
R22|e0/2|IPv6|ac10:ffff:0:4::1/64|ac10:ffff:0:4::/64|to R23

### Часть 2: Задокументируем выделенные IP-адреса.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R14|Lo0|IPv4|172.16.0.14/32|172.16.0.0/24|Сеть управления Москва
R14|Lo0|IPv6|ac10:ffff:0:10a1::14/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R15|Lo0|IPv4|172.16.0.15/32|172.16.0.0/24|Сеть управления Москва
R15|Lo0|IPv6|ac10:ffff:0:10a1::15/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R18|Lo0|IPv4|172.16.1.18/24|172.16.1.0/24|Сеть управления С.-Петербург
R18|Lo0|IPv6|ac10:ffff:0:2a42::18/128|ac10:ffff:0:2a42::/64|Сеть управления С.-Петербург
R21|Lo0|IPv4|172.16.5.21/32|172.16.5.0/24|Сеть управления Ламас
R21|Lo0|IPv6|ac10:ffff:0:60a6::221/128|ac10:ffff:0:60a6::/64|Сеть управления Ламас
R22|Lo0|IPv4|172.16.6.22/32|172.16.6.0/24|Сеть управления Киторн
R22|Lo0|IPv6|ac10:ffff:0:70a7::22/128|ac10:ffff:0:70a7::/64|Сеть управления Киторн

#### 1.3 Схема.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab11/lab11.png)

#### 1.4 Конфигурации оборудования.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab11/Configs).

### Часть 2: Настроим фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).

R14 - Добавим `filter-list` в сторону R22.
```
ip as-path access-list 1 permit ^$
ip as-path access-list 1 deny .*
router bgp 1001
 address-family ipv4
  neighbor 1.1.0.1 filter-list 1 out
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:1::1 filter-list 1 out
 exit
exit
```
R15 - Добавим `filter-list` в сторону R21.
```
ip as-path access-list 1 permit ^$
ip as-path access-list 1 deny .*
router bgp 1001
 address-family ipv4
  neighbor 1.1.0.5 filter-list 1 out
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:2::1 filter-list 1 out
 exit
exit
```
### Часть 3: Настроим фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).

R18 - Добавим `prefix-list` в сторону R24 и R26.
```
ip prefix-list LIST_OUT1 seq 5 permit 172.16.1.0/24
ip prefix-list LIST_OUT1 seq 10 permit 192.168.2.0/23
ip prefix-list LIST_OUT1 seq 15 deny 0.0.0.0/0 le 32
ipv6 prefix-list LIST_OUT2 seq 5 permit AC10:FFFF:0:2A42::/64
ipv6 prefix-list LIST_OUT2 seq 10 permit AC10:FFFF:0:2B42::/64
ipv6 prefix-list LIST_OUT2 seq 15 permit AC10:FFFF:0:2C42::/64
ipv6 prefix-list LIST_OUT2 seq 20 deny ::/0 le 128
router bgp 2042
 address-family ipv4
  neighbor 1.1.0.25 prefix-list LIST_OUT1 out
  neighbor 1.1.0.33 prefix-list LIST_OUT1 out
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:6::1 prefix-list LIST_OUT2 out
  neighbor AC10:FFFF:0:7::1 prefix-list LIST_OUT2 out
 exit
exit
```
### Часть 4: Настроим провайдер Киторн так, чтобы в офис Москва отдавался только маршрут по-умолчанию.

R22 - Добавим `default-originate` в сторону R14.
```
router bgp 101
 address-family ipv4
  neighbor 1.1.0.2 default-originate
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:1::2 default-originate
 exit
exit
```
R14 - Уберем статические маршруты, что бы они приходили от провайдера Китрон и уберем увеличение метрики в ospf, что бы ospf не направлял весь трафик на R15.
```
no ip route 0.0.0.0 0.0.0.0 1.1.0.1
no ipv6 route ::/0 AC10:FFFF:0:1::1 110
router ospf 1
no default-information originate metric 5
default-information originate
exit
ipv6 router ospf 1
no default-information originate metric 5
default-information originate
exit
```
### Часть 5: Настроим провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по-умолчанию и префикс офиса С.-Петербург.

R21 -  - Добавим `default-originate` и `route-map` в сторону R15. 
```
ip prefix-list LIST_OUT_SP seq 5 permit 172.16.1.0/24
ip prefix-list LIST_OUT_SP seq 10 permit 192.168.2.0/23
ip prefix-list LIST_OUT_SP seq 15 deny 0.0.0.0/0 le 32
ipv6 prefix-list LIST_OUT_SP_ipv6 seq 5 permit AC10:FFFF:0:2A42::/64
ipv6 prefix-list LIST_OUT_SP_ipv6 seq 10 permit AC10:FFFF:0:2B42::/64
ipv6 prefix-list LIST_OUT_SP_ipv6 seq 15 permit AC10:FFFF:0:2C42::/64
ipv6 prefix-list LIST_OUT_SP_ipv6 seq 20 deny ::/0 le 128
route-map DOWNLINK-Moscow_ipv6 permit 10
 match ipv6 address prefix-list LIST_OUT_SP_ipv6
exit
route-map DOWNLINK-Moscow permit 10
 match ip address prefix-list LIST_OUT_SP
exit
router bgp 301
 address-family ipv4
  neighbor 1.1.0.6 default-originate
  neighbor 1.1.0.6 route-map DOWNLINK-Moscow out
 exit
  neighbor AC10:FFFF:0:2::2 default-originate
  neighbor AC10:FFFF:0:2::2 route-map DOWNLINK-Moscow_ipv6 out
 exit
exit
``` 
R15 - Уберем статические маршруты, что бы они приходили от провайдера Ламас и добавим увеличение метрики в ospf для сетей Чокурдах, что бы ospf направлял этот трафик на R14.
```
no ip route 0.0.0.0 0.0.0.0 1.1.0.5
no ip route 0.0.0.0 0.0.0.0 1.1.0.5
ip prefix-list Chokurdah seq 5 permit 192.168.4.0/23
ip prefix-list Chokurdah seq 10 permit 172.16.2.0/24
ipv6 prefix-list Chokurdah-ipv6 seq 5 permit AC10:FFFF:0:30A3::/64
ipv6 prefix-list Chokurdah-ipv6 seq 10 permit AC10:FFFF:0:30B3::/64
ipv6 prefix-list Chokurdah-ipv6 seq 15 permit AC10:FFFF:0:30C3::/64
route-map RM-Chokurdah-out permit 10
 match ip address prefix-list Chokurdah
 match ipv6 address prefix-list Chokurdah-ipv6
exit
router ospf 1
 default-information originate metric 5 route-map RM-Chokurdah-out
exit
ipv6 router ospf 1
 default-information originate metric 5 route-map RM-Chokurdah-out
exit
```
### Часть 6: Проверим IP связность.

#### Посмотрим текущие IP адеса на машинах VPC в офисах.

VPC1
```
PC1 : 192.168.0.4 255.255.255.0 gateway 192.168.0.1
PC1 : ac10:ffff:0:10b1:2050:79ff:fe66:6801/64
```
VPC7
```
PC1 : 192.168.1.4 255.255.255.0 gateway 192.168.1.1
PC1 : ac10:ffff:0:10c1:2050:79ff:fe66:6807/64
```
VPC8
```
PC1 : 192.168.2.2 255.255.255.0 gateway 192.168.2.1
PC1 : ac10:ffff:0:2b42:2050:79ff:fe66:6808/64
```
VPC
```
PC1 : 192.168.3.2 255.255.255.0 gateway 192.168.3.1
PC1 : ac10:ffff:0:2c42:2050:79ff:fe66:680b/64
```
VPC30
```
PC1 : 192.168.4.2 255.255.255.0 gateway 192.168.4.1
PC1 : ac10:ffff:0:30b3:2050:79ff:fe66:681e/64
```
VPC31
```
PC1 : 192.168.5.2 255.255.255.0 gateway 192.168.5.1
PC1 : ac10:ffff:0:30c3:2050:79ff:fe66:681f/64
```

#### Проверим выборочно связанность между всеми VPC.


