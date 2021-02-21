# IPSec over DmVPN

## Цель:

1. Настроить GRE поверх IPSec между офисами Москва и С.-Петербург

2. Настроите DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги

3. Все узлы в офисах в лабораторной работе должны иметь IP связность

## Решение:

1. Предоставим таблицы стыковочных сетей, выделенных IP-адресов, схему и конфигурации оборудования. 
  
1.1 Таблица стыковочных сетей.
  
1.2 Таблица выделенных IP-адресов.
  
1.3 Схема.
  
1.4 Конфигурации оборудования.

2. Настроим GRE поверх IPSec между офисами Москва и С.-Петербург

3. Настроим DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги

4. Проверим IP связанность

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

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab14/lab14.png)

#### 1.4 Конфигурации оборудования.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab14/Configs).

### Часть 2: Настроим GRE поверх IPSec между офисами Москва и С.-Петербург

R18
```
crypto isakmp policy 100 
authentication pre-share
encryption 3des
group 2
hash md5
lifetime 900
exit
crypto isakmp key cisco address 172.16.0.14
crypto isakmp key cisco address 172.16.0.15
crypto ipsec transform-set GRE_IPSEC esp-des
mode transport
exit
crypto ipsec profile TO-MSK
 set security-association lifetime seconds 900
 set transform-set GRE_IPSEC
 exit
interface Tunnel 100
 tunnel protection ipsec profile TO-MSK
 no keepalive 3 3
 exit
interface Tunnel 101
 tunnel protection ipsec profile TO-MSK
 no keepalive 3 3
 exit
```
R14
```
crypto isakmp policy 100 
authentication pre-share
encryption 3des
group 2
hash md5
lifetime 900
exit
crypto isakmp key cisco address 172.16.1.18
crypto ipsec transform-set GRE_IPSEC esp-des
mode transport
exit
crypto ipsec profile TO-SPB
 set security-association lifetime seconds 900
 set transform-set GRE_IPSEC
 exit
interface Tunnel 100
 tunnel protection ipsec profile TO-SPB
 no keepalive 3 3
 exit
```
R15
```
crypto isakmp policy 100 
authentication pre-share
encryption 3des
group 2
hash md5
lifetime 900
exit
crypto isakmp key cisco address 172.16.1.18
crypto ipsec transform-set GRE_IPSEC esp-des
mode transport
exit
crypto ipsec profile TO-SPB
 set security-association lifetime seconds 900
 set transform-set GRE_IPSEC
 exit
interface Tunnel 101
 tunnel protection ipsec profile TO-SPB
 no keepalive 3 3
 exit
```
Проверим работу командой `sh crypto isakmp sa` на R14,R15 и R18.

R14
```
R14#sh crypto isakmp sa
IPv4 Crypto ISAKMP SA
dst             src             state          conn-id status
172.16.0.14     172.16.1.18     QM_IDLE           1002 ACTIVE

IPv6 Crypto ISAKMP SA
```
R15
```
R15#sh crypto isakmp sa
IPv4 Crypto ISAKMP SA
dst             src             state          conn-id status
172.16.0.15     172.16.1.18     QM_IDLE           1005 ACTIVE

IPv6 Crypto ISAKMP SA
```
R18
```
R18#sh crypto isakmp sa
IPv4 Crypto ISAKMP SA
dst             src             state          conn-id status
172.16.0.15     172.16.1.18     QM_IDLE           1006 ACTIVE
172.16.0.14     172.16.1.18     QM_IDLE           1007 ACTIVE

IPv6 Crypto ISAKMP SA
```
### Часть 3: Настроим DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги

