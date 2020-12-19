# BGP. Основы

## Цель:

1. Настроить eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас.

2. Настроите eBGP между провайдерами Киторн и Ламас.

3. Настроите eBGP между Ламас и Триада.

4. Настроить eBGP между офисом С.-Петербург и провайдером Триада.

5. Организовать IP доступность между офисами Москва и С.-Петербург.

## Решение:

1. Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 
  
1.1 Таблица стыковочных сетей.
  
1.2 Таблица выделенных IP-адресов.
  
1.3 Схема.
  
1.4 Конфигурации оборудования.

2. Присвоим автономные системы.

3. Настроим eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас. Между Киторн и Ламас. Между Ламас и Триада. Между офисом С.-Петербург и провайдером Триада.

4. Проверим IP доступность между офисами Москва и С.-Петербург.

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
R21|Lo0|IPv4|172.16.5.21/32|172.16.5.0/24|Сеть управления Ламас
R21|Lo0|IPv6|ac10:ffff:0:60a6::221/128|ac10:ffff:0:60a6::/64|Сеть управления Ламас
R22|Lo0|IPv4|172.16.6.22/32|172.16.6.0/24|Сеть управления Киторн
R22|Lo0|IPv6|ac10:ffff:0:70a7::22/128|ac10:ffff:0:70a7::/64|Сеть управления Киторн
R24|Lo0|IPv4|172.16.4.24/32|172.16.4.0/24|Сеть управления Триада
R24|Lo0|IPv6|ac10:ffff:0:50a5::24/128|ac10:ffff:0:50a5::/64|Сеть управления Триада
R26|Lo0|IPv4|172.16.4.26/32|172.16.4.0/24|Сеть управления Триада
R26|Lo0|IPv6|ac10:ffff:0:50a5::26/128|ac10:ffff:0:50a5::/64|Сеть управления Триада

#### 1.3 Схема.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab09/lab09.png)

#### 1.4 Конфигурации оборудования.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab09/Configs).

### Часть 2: Присвоим автономные системы.

Москва - 10
С.-Петербург - 20
Триада - 50
Ламас - 60
Киторн - 70

### Часть 3: Настроим eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас. Между Киторн и Ламас. Между Ламас и Триада. Между офисом С.-Петербург и провайдером Триада.

R14
```
router bgp 10
 bgp router-id 14.14.14.14
 neighbor 1.1.0.1 remote-as 70
 neighbor AC10:FFFF:0:1::1 remote-as 70
 address-family ipv4
  network 172.16.0.0 mask 255.255.255.0
  network 192.168.0.0
  network 192.168.1.0
  neighbor 1.1.0.1 activate
 exit
 address-family ipv6
  network AC10:FFFF:0:10A1::/64
  network AC10:FFFF:0:10B1::/64
  network AC10:FFFF:0:10C1::/64
  neighbor AC10:FFFF:0:1::1 activate
 exit
```
R15
```
router bgp 10
 bgp router-id 15.15.15.15
 neighbor 1.1.0.5 remote-as 60
 neighbor AC10:FFFF:0:2::1 remote-as 60
 address-family ipv4
  network 172.16.0.0 mask 255.255.255.0
  network 192.168.0.0
  network 192.168.1.0
  neighbor 1.1.0.5 activate
 exit
 address-family ipv6
  network AC10:FFFF:0:10A1::/64
  network AC10:FFFF:0:10B1::/64
  network AC10:FFFF:0:10C1::/64
  neighbor AC10:FFFF:0:2::1 activate
 exit
```
R18
```
router bgp 20
 bgp router-id 18.18.18.18
 neighbor 1.1.0.25 remote-as 50
 neighbor 1.1.0.33 remote-as 50
 neighbor AC10:FFFF:0:6::1 remote-as 50
 neighbor AC10:FFFF:0:7::1 remote-as 50
 address-family ipv4
  network 172.16.1.0 mask 255.255.255.0
  network 192.168.2.0 mask 255.255.254.0
  neighbor 1.1.0.25 activate
  neighbor 1.1.0.33 activate
 exit
 address-family ipv6
  network AC10:FFFF:0:2A42::/64
  network AC10:FFFF:0:2B42::/64
  network AC10:FFFF:0:2C42::/64
  neighbor AC10:FFFF:0:6::1 activate
  neighbor AC10:FFFF:0:7::1 activate
 exit
ip route 0.0.0.0 0.0.0.0 1.1.0.25 105
ip route 0.0.0.0 0.0.0.0 1.1.0.33 110
ipv6 route ::/0 AC10:FFFF:0:6::1 105
ipv6 route ::/0 AC10:FFFF:0:7::1 110
```
R21
```
router bgp 60
 bgp router-id 21.21.21.21
 neighbor 1.1.0.6 remote-as 10
 neighbor 1.1.0.9 remote-as 70
 neighbor 1.1.0.18 remote-as 50
 neighbor AC10:FFFF:0:2::2 remote-as 10
 neighbor AC10:FFFF:0:3::1 remote-as 70
 neighbor AC10:FFFF:0:5::2 remote-as 50
 address-family ipv4
  neighbor 1.1.0.6 activate
  neighbor 1.1.0.9 activate
  neighbor 1.1.0.18 activate
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:2::2 activate
  neighbor AC10:FFFF:0:3::1 activate
  neighbor AC10:FFFF:0:5::2 activate
 exit
```
R22
```
router bgp 70
 bgp router-id 22.22.22.22
 neighbor 1.1.0.2 remote-as 10
 neighbor 1.1.0.10 remote-as 60
 neighbor AC10:FFFF:0:1::2 remote-as 10
 neighbor AC10:FFFF:0:3::2 remote-as 60
 address-family ipv4
  neighbor 1.1.0.2 activate
  neighbor 1.1.0.10 activate
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:1::2 activate
  neighbor AC10:FFFF:0:3::2 activate
 exit
```
R24
```
router bgp 50
 bgp router-id 24.24.24.24
 neighbor 1.1.0.17 remote-as 60
 neighbor 1.1.0.26 remote-as 20
 neighbor 172.16.4.26 remote-as 50
 neighbor 172.16.4.26 update-source Loopback0
 neighbor AC10:FFFF:0:5::1 remote-as 60
 neighbor AC10:FFFF:0:6::2 remote-as 20
 neighbor AC10:FFFF:0:5035::2 remote-as 50
 address-family ipv4
  neighbor 1.1.0.17 activate
  neighbor 1.1.0.26 activate
  neighbor 172.16.4.26 activate
  neighbor 172.16.4.26 route-reflector-client
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:5::1 activate
  neighbor AC10:FFFF:0:6::2 activate
  neighbor AC10:FFFF:0:5035::2 activate
 exit
 ```
 R26
 ```
 router bgp 50
 bgp router-id 26.26.26.26
 neighbor 1.1.0.34 remote-as 20
 neighbor 172.16.4.24 remote-as 50
 neighbor 172.16.4.24 update-source Loopback0
 neighbor AC10:FFFF:0:7::2 remote-as 20
 neighbor AC10:FFFF:0:5035::1 remote-as 50
 address-family ipv4
  neighbor 1.1.0.34 activate
  neighbor 172.16.4.24 activate
 exit
 address-family ipv6
  neighbor AC10:FFFF:0:7::2 activate
  neighbor AC10:FFFF:0:5035::1 activate
 exit
 ```
### Часть 4: Проверим IP доступность между офисами Москва и С.-Петербург.




