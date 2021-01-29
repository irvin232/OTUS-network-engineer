# Основные протоколы сети интернет

## Цель:

1. Настроить NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.

2. Настроить NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.

3. Настроить статический NAT для R20.

4. Настроить NAT так, чтобы R19 был доступен с любого узла для удаленного управления.

5. Настроить для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.

6. Настроить NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.

## Решение:

1. Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 
  
1.1 Таблица стыковочных сетей.
  
1.2 Таблица выделенных IP-адресов.
  
1.3 Схема.
  
1.4 Конфигурации оборудования.

2. Настроим NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.

3. Настроим NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.

4. Настроим статический NAT для R20.

5. Настроим NAT так, чтобы R19 был доступен с любого узла для удаленного управления.

6. Настроим для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.

7. Настроим NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.

### Часть 1: Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 

#### 1.1 Таблица стыковочных сетей.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
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

### Часть 2: Задокументируем выделенные IP-адреса.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
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
R14|Lo0|IPv4|172.16.0.14/32|172.16.0.0/24|Сеть управления Москва
R14|Lo0|IPv6|ac10:ffff:0:10a1::14/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R15|Lo0|IPv4|172.16.0.15/32|172.16.0.0/24|Сеть управления Москва
R15|Lo0|IPv6|ac10:ffff:0:10a1::15/128|ac10:ffff:0:10a1::/64|Сеть управления Москва
R18|Lo0|IPv4|172.16.1.18/24|172.16.1.0/24|Сеть управления С.-Петербург
R18|Lo0|IPv6|ac10:ffff:0:2a42::18/128|ac10:ffff:0:2a42::/64|Сеть управления С.-Петербург

#### 1.3 Схема.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab12/lab12.png)

#### 1.4 Конфигурации оборудования.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab12/Configs).

### Часть 2: Настроим NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001

Выделим внешнюю адресацию NAT для R14: 100.0.14.0/24 Определим какие интерфейсы для NAT внешние и внутренние. Добавим статический маршрут внешней NAT адресации на Null-интерфейс. Настроим NAT with overload в один адрес для пользовательских сетей. Объявим внешнюю NAT адресацию в BGP. Удалим объявление пользовательских сетей из BGP. И добавим prefix-list на блокировку анонсов внешних NAT сетей по iBGP.

R14
```
interface Ethernet0/0
ip nat inside
exit
interface Ethernet0/1
ip nat inside
exit
interface Ethernet0/2
ip nat outside
exit
interface Ethernet0/3
ip nat inside
exit
ip route 100.0.14.0 255.255.255.0 null 0
ip nat pool NAT-MOSCOW 100.0.14.1 100.0.14.1 netmask 255.255.255.0
ip nat inside source list 1 pool NAT-MOSCOW overload
access-list 1 permit 192.168.0.0 0.0.1.255
router bgp 1001
 address-family ipv4
  network 100.0.14.0 mask 255.255.255.0
  no network 192.168.0.0
  no network 192.168.1.0
  neighbor 172.16.0.15 prefix-list NO_NAT_FROM_iBGP out
  exit
 exit
ip prefix-list NO_NAT_FROM_iBGP seq 5 deny 100.0.15.0/24
ip prefix-list NO_NAT_FROM_iBGP seq 10 deny 100.0.14.0/24
ip prefix-list NO_NAT_FROM_iBGP seq 15 permit 0.0.0.0/0
```
Выделим внешнюю адресацию NAT для R15: 100.0.15.0/24 Определим какие интерфейсы для NAT внешние и внутренние. Добавим статический маршрут внешней NAT адресации на Null-интерфейс. Настроим NAT with overload в один адрес для пользовательских сетей. Объявим внешнюю NAT адресацию в BGP. Удалим объявление пользовательских сетей из BGP. И добавим prefix-list на блокировку анонсов внешних NAT сетей по iBGP.

R15
```
interface Ethernet0/0
ip nat inside
exit
interface Ethernet0/1
ip nat inside
exit
interface Ethernet0/2
ip nat outside
exit
interface Ethernet0/3
ip nat inside
exit
ip route 100.0.15.0 255.255.255.0 null 0
ip nat pool NAT-MOSCOW 100.0.15.1 100.0.15.1 netmask 255.255.255.0
ip nat inside source list 1 pool NAT-MOSCOW overload
access-list 1 permit 192.168.0.0 0.0.1.255
router bgp 1001
 address-family ipv4
  network 100.0.15.0 mask 255.255.255.0
  no network 192.168.0.0
  no network 192.168.1.0
  neighbor 172.16.0.14 prefix-list NO_NAT_FROM_iBGP out
  exit
 exit
ip prefix-list NO_NAT_FROM_iBGP seq 5 deny 100.0.15.0/24
ip prefix-list NO_NAT_FROM_iBGP seq 10 deny 100.0.14.0/24
ip prefix-list NO_NAT_FROM_iBGP seq 15 permit 0.0.0.0/0
```
Проверим работу NAT. Пропингуем c VPC Москвы VPC в офисе Чокурдах и посмотрим вывод команды `sh ip nat translations` на R15

VPC7
```
VPCS> ping 192.168.5.2

84 bytes from 192.168.5.2 icmp_seq=1 ttl=58 time=3.518 ms
84 bytes from 192.168.5.2 icmp_seq=2 ttl=58 time=2.622 ms
84 bytes from 192.168.5.2 icmp_seq=3 ttl=58 time=2.927 ms
84 bytes from 192.168.5.2 icmp_seq=4 ttl=58 time=2.685 ms
84 bytes from 192.168.5.2 icmp_seq=5 ttl=58 time=2.182 ms
```
R15
```
R15#sh ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- 100.0.15.20        1.1.1.22           ---                ---
tcp 100.0.15.19:2222   172.16.0.19:22     ---                ---
icmp 100.0.15.1:24410  192.168.1.4:24410  192.168.5.2:24410  192.168.5.2:24410
icmp 100.0.15.1:24666  192.168.1.4:24666  192.168.5.2:24666  192.168.5.2:24666
icmp 100.0.15.1:24922  192.168.1.4:24922  192.168.5.2:24922  192.168.5.2:24922
icmp 100.0.15.1:25178  192.168.1.4:25178  192.168.5.2:25178  192.168.5.2:25178
icmp 100.0.15.1:25434  192.168.1.4:25434  192.168.5.2:25434  192.168.5.2:25434
R15#
```

### Часть 3: Настроим NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.

Выделим внешнюю адресацию NAT для R18: 100.0.18.0/24 Определим какие интерфейсы для NAT внешние и внутренние. Добавим статический маршрут внешней NAT адресации на Null-интерфейс. Настроим NAT with overload в один адрес для пользовательских сетей. Объявим внешнюю NAT адресацию в BGP. Удалим объявление пользовательских сетей из BGP. Удалим из prefix-list LIST_OUT1 пользовательские сети и добавим внешнюю NAT адресацию.

R18
```
interface Ethernet0/0
ip nat inside
exit
interface Ethernet0/1
ip nat inside
exit
interface Ethernet0/2
ip nat outside
exit
interface Ethernet0/3
ip nat outside
exit
ip route 100.0.18.0 255.255.255.0 null 0
ip nat pool NAT-SP 100.0.18.1 100.0.18.5 netmask 255.255.255.0
ip nat inside source list 1 pool NAT-SP overload
access-list 1 permit 192.168.2.0 0.0.1.255
router bgp 2042
 address-family ipv4
  network 100.0.18.0 mask 255.255.255.0
  no network 192.168.2.0 mask 255.255.254.0
  exit
 exit
no ip prefix-list LIST_OUT1 seq 10 permit 192.168.2.0/23
ip prefix-list LIST_OUT1 seq 10 permit 100.0.18.0/24
```
Проверим работу NAT. Пропингуем c VPC С.-Петербурга VPC в офисе Чокурдах и посмотрим вывод команды `sh ip nat translations` на R18

VPC
```
VPCS> ping 192.168.4.2

84 bytes from 192.168.4.2 icmp_seq=1 ttl=58 time=3.711 ms
84 bytes from 192.168.4.2 icmp_seq=2 ttl=58 time=1.604 ms
84 bytes from 192.168.4.2 icmp_seq=3 ttl=58 time=2.335 ms
84 bytes from 192.168.4.2 icmp_seq=4 ttl=58 time=1.607 ms
84 bytes from 192.168.4.2 icmp_seq=5 ttl=58 time=2.047 ms
```
R18
```
R18#sh ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
icmp 100.0.18.1:43611  192.168.3.2:43611  192.168.4.2:43611  192.168.4.2:43611
icmp 100.0.18.1:43867  192.168.3.2:43867  192.168.4.2:43867  192.168.4.2:43867
icmp 100.0.18.1:44123  192.168.3.2:44123  192.168.4.2:44123  192.168.4.2:44123
icmp 100.0.18.1:44379  192.168.3.2:44379  192.168.4.2:44379  192.168.4.2:44379
icmp 100.0.18.1:44635  192.168.3.2:44635  192.168.4.2:44635  192.168.4.2:44635
```

### Часть 4: Настроим статический NAT для R20.

Интерфейс e0/3 мы пометили как внутренний ранее, поэтому прописываем только статический NAT.

R15
```
ip nat inside source static 1.1.1.22 100.0.15.20
```
Проверим работу NAT. Посмотрим вывод команды `sh ip nat translations` на R15

R15
```
R15#sh ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- 100.0.15.20        1.1.1.22           ---                ---
tcp 100.0.15.19:2222   172.16.0.19:22     ---                ---
```

### Часть 5: Настроим NAT так, чтобы R19 был доступен с любого узла для удаленного управления

Настроим `secondary` интерфейсы на R14 и R15. Прописываем на обоих статическую PAT-трансляцию, что бы устройство было доступно через оба маршрутизатора, хотя и под разными IP.

R14
```
interface Ethernet0/2
ip address 100.0.14.19 255.255.255.0 secondary
exit
ip nat inside source static tcp 172.16.0.19 22 100.0.14.19 2222
```
R15
```
interface Ethernet0/2
ip address 100.0.15.19 255.255.255.0 secondary
exit
ip nat inside source static tcp 172.16.0.19 22 100.0.15.19 2222
```
Проверим работу NAT. Пустим команду telnet на 2222 порт с роутера С.-Петербурга. Посмотрим вывод команды `sh ip nat translations` на R14.
SW10
```
SW10#telnet 100.0.14.19 2222 /source-interface vlan22
Trying 100.0.14.19, 2222 ... Open
SSH-2.0-Cisco-1.25

[Connection to 100.0.14.19 closed by foreign host]
```
R14
```
R14#sh ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
tcp 100.0.14.19:2222   172.16.0.19:22     100.0.18.1:64066   100.0.18.1:64066
tcp 100.0.14.19:2222   172.16.0.19:22     ---                ---
```

### Часть 6: Настроим DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP

R12
```
ip dhcp excluded-address 192.168.0.1 192.168.0.3
ip dhcp pool POOL-11_VLAN
network 192.168.0.0 255.255.255.0
default-router 192.168.0.1
exit
ipv6 dhcp pool POOL-IPV6-11_VLAN

```
R13
```
ip dhcp excluded-address 192.168.1.1 192.168.1.3
ip dhcp pool POOL-12_VLAN
network 192.168.1.0 255.255.255.0
default-router 192.168.1.1
exit
```
На VPC1 и VPC7
```
ip dhcp
```
Проверим получение IP на примере VPC7.
```
VPCS> dhcp
DORA IP 192.168.1.5/24 GW 192.168.1.1
```

### Часть 7. Настроим NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13

R12 и R13
```
clock timezone MSK 3
clock calendar-valid
ntp master 3
```
На всех остальных устройствах вводим.
```
ntp server 172.16.0.12
ntp server 172.16.0.13
```
Проверим NTP командой `show ntp status` на примере R15.
```
R15#show ntp status
Clock is synchronized, stratum 4, reference is 172.16.0.12
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 772100 (1/100 of seconds), resolution is 4000
reference time is E3BEDCF9.8AC08490 (21:14:01.542 EET Fri Jan 29 2021)
clock offset is 0.5000 msec, root delay is 1.00 msec
root dispersion is 9.41 msec, peer dispersion is 1.97 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000008 s/s
system poll interval is 1024, last update was 253 sec ago.
```
