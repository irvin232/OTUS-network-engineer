# Архитектура сети 

## Цель: 
В данной работе необходимо распланировать адресное пространство. Настроить IP на всех активных портах для дальнейшей работы над проектом. Используем ipv4 и ipv6.

## Решение:
  1. Разработаем и задокументируем адресное пространство для лабораторного стенда.
  2. Задокументируем стыковочные сети.
  3. Задокументируем выделенные IP-адреса.
  4. Ссылка на конфигурации оборудования, основные шаги настройки оборудования и приведем примеры настройки.
  5. Итоговая схема.

### Часть 1: Разработаем и задокументируем адресное пространство для лабораторного стенда.

  IPv4
  
  1.1.0.0/21 – Стыковочные сети.
  
  172.16.0.0/21 – Сети управления. Последний актет на каждом оборудовании сопадает с цифрой в имени оборудования.
  
  192.168.0.0/21 – Пользовательские сети для VPC.
  
  IPv6
  
  ac10:ffff::/48 - для каждого сегмента выделяем посеть с /64 префиксом. Последний актет на каждом оборудовании сопадает с цифрой в имени оборудования.
  
  FE80::* - link-local адреса совпадают с цифрой в имени оборудования.
  
### Часть 2: Задокументируем стыковочные сети.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R22|e0/0|IPv4|1.1.0.1|1.1.0.0/30|to R14
R22|e0/0|IPv6|ac10:ffff:0:1::1/64|ac10:ffff:0:1::/64|to R14
R22|e0/1|IPv4|1.1.0.9|1.1.0.8/30|to R21
R22|e0/1|IPv6|ac10:ffff:0:3::1/64|ac10:ffff:0:3::/64|to R21
R22|e0/2|IPv4|1.1.0.13|1.1.0.12/30|to R23
R22|e0/2|IPv6|ac10:ffff:0:4::1/64|ac10:ffff:0:4::/64|to R23
R21|e0/0|IPv4|1.1.0.5|1.1.0.4/30|to R15
R21|e0/0|IPv6|ac10:ffff:0:2::1/64|ac10:ffff:0:2::/64|to R15
R21|e0/1|IPv4|1.1.0.10|1.1.0.8/30|to R22
R21|e0/1|IPv6|ac10:ffff:0:3::2/64|ac10:ffff:0:3::/64|to R22
R21|e0/2|IPv4|1.1.0.17|1.1.0.16/29|to R24
R21|e0/2|IPv6|ac10:ffff:0:5::1/64|ac10:ffff:0:5::/64|to R24
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
R27|e0/0|IPv4|1.1.0.42|1.1.0.40/29|to R25
R27|e0/0|IPv6|ac10:ffff:0:8::2/64|ac10:ffff:0:8::/64|to R25
R28|e0/1|IPv4|1.1.0.50|1.1.0.48/29|to R25
R28|e0/1|IPv6|ac10:ffff:0:9::2/64|ac10:ffff:0:9::/64|to R25
R28|e0/1|IPv4|1.1.0.58|1.1.0.56/29|to R26
R28|e0/1|IPv6|ac10:ffff:0:10::2/64|ac10:ffff:0:10::/64|to R26
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

### Часть 2: Задокументируем выделенные IP-адреса.

Name|Port|Protocol|Address|Network|Description
----|----|--------|-------|-------|-----------
R22|Lo0|IPv4|172.16.6.22/32|172.16.6.0/24|Сеть управления Киторн
R22|Lo0|IPv6|ac10:ffff:0:70a7::22/128|ac10:ffff:0:70a7::/64|Сеть управления Киторн
R21|Lo0|IPv4|172.16.5.21/32|172.16.5.0/24|Сеть управления Ламас
R21|Lo0|IPv6|ac10:ffff:0:60a6::221/128|ac10:ffff:0:60a6::/64|Сеть управления Ламас
R23|Lo0|IPv4|172.16.4.23/32|172.16.4.0/24|Сеть управления Триада
R23|Lo0|IPv6|ac10:ffff:0:50a5::23/128|ac10:ffff:0:50a5::/64|Сеть управления Триада
R24|Lo0|IPv4|172.16.4.24/32|172.16.4.0/24|Сеть управления Триада
R24|Lo0|IPv6|ac10:ffff:0:50a5::24/128|ac10:ffff:0:50a5::/64|Сеть управления Триада
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
SW4|Int vlan 10|IPv4|172.16.0.4/24|172.16.0.0/24|Сеть управления Москва
SW4|Int vlan 10|IPv6|ac10:ffff:0:10a1::4/64|ac10:ffff:0:10a1::/64|Сеть управления Москва
SW5|Int vlan 10|IPv4|172.16.0.5/24|172.16.0.0/24|Сеть управления Москва
SW5|Int vlan 10|IPv6|ac10:ffff:0:10a1::5/64|ac10:ffff:0:10a1::/64|Сеть управления Москва
SW3|Int vlan 10|IPv4|172.16.0.3/24|172.16.0.0/24|Сеть управления Москва
SW3|Int vlan 10|IPv6|ac10:ffff:0:10a1::3/64|ac10:ffff:0:10a1::/64|Сеть управления Москва
SW2|Int vlan 10|IPv4|172.16.0.2/24|172.16.0.0/24|Сеть управления Москва
SW2|Int vlan 10|IPv6|ac10:ffff:0:10a1::2/64|ac10:ffff:0:10a1::/64|Сеть управления Москва

### Часть 4: Ссылка на конфигурации оборудования, основные шаги настройки оборудования и приведем примеры настройки.

[Итоговые конфигурации оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab04/Configs).

#### Основные шаги для маршрутизаторов:
  - Включаем ipv6 unicast-routing.
  - Присваиваем IPv4 и IPv6 адреса на интерфейсах.
  - Включаем интерфейсы.
#### Основные шаги для коммутаторов:
  - Добавляем VLAN-ы.
  - Настраиваем uplink-и в trunk.
  - Настраиваем интерфейсы для VPC в access.
  - Присваиваем IPv4 и IPv6 адреса на VLAN интерфейсах.
#### Основные шаги для VPC:
  - Присваиваем IPv4 адреса для VPC статикой, а для IPv6 способом SLAAC.

#### Пример настройки маршрутизатора R12, на котором терминируются шлюзы Москвы и настроен HSRP с R13.
````
ipv6 unicast-routing
interface Ethernet0/0
 no ip address
 ipv6 address FE80::12 link-local
 no shutdown
exit
interface Ethernet0/0.10
 description vlan 10
 encapsulation dot1Q 10
 ip address 172.16.0.12 255.255.255.0
 standby version 2
 standby 1 ip 172.16.0.1
 standby 1 priority 50
 standby 1 preempt
 standby 2 ipv6 autoconfig
 standby 2 priority 50
 standby 2 preempt
 ipv6 address AC10:FFFF:0:10A1::12/64
exit
interface Ethernet0/0.11
 description vlan 11
 encapsulation dot1Q 11
 ip address 192.168.0.3 255.255.255.0
 standby version 2
 standby 1 ip 192.168.0.1
 standby 1 priority 50
 standby 1 preempt
 standby 2 ipv6 autoconfig
 standby 2 priority 50
 standby 2 preempt
 ipv6 address AC10:FFFF:0:10B1::12/64
exit
interface Ethernet0/0.12
 description vlan 12
 encapsulation dot1Q 12
 ip address 192.168.1.3 255.255.255.0
 standby version 2
 standby 3 ip 192.168.1.1
 standby 3 priority 50
 standby 3 preempt
 standby 4 ipv6 autoconfig
 standby 4 priority 50
 standby 4 preempt
 ipv6 address AC10:FFFF:0:10C1::12/64
exit
interface Ethernet0/1
 ip address 1.1.1.25 255.255.255.252
 ipv6 address FE80::12 link-local
 no shutdown
exit
interface Ethernet0/2
 ip address 1.1.1.6 255.255.255.252
 ipv6 address FE80::12 link-local
 no shutdown
exit
interface Ethernet0/3
 ip address 1.1.1.14 255.255.255.252
 ipv6 address FE80::12 link-local
 no shutdown
exit
````
#### Пример настройки маршрутизатора R14, с менеджментом на Loopback0 интерфейсе.
````
ipv6 unicast-routing
interface Loopback0
 ip address 172.16.0.14 255.255.255.255
 ipv6 address AC10:FFFF:0:10A1::14/128
 no shutdown
exit
interface Ethernet0/0
 ip address 1.1.1.5 255.255.255.252
 ipv6 address FE80::14 link-local
 no shutdown
exit
interface Ethernet0/1
 ip address 1.1.1.9 255.255.255.252
 ipv6 address FE80::14 link-local
 no shutdown
exit
interface Ethernet0/2
 ip address 1.1.0.2 255.255.255.252
 ipv6 address FE80::14 link-local
 no shutdown
exit
interface Ethernet0/3
 ip address 1.1.1.1 255.255.255.252
 ipv6 address FE80::14 link-local
  no shutdown
exit
````
#### Пример настройки коммутатора SW4.
````
vlan 10
name MGMT
vlan 11
name VPC1
vlan 12
name VPC7
exit
interface Port-channel1
 switchport trunk allowed vlan 10-12
 switchport trunk encapsulation dot1q
 switchport mode trunk
exit
interface Ethernet0/0
 switchport trunk allowed vlan 10-12
 switchport trunk encapsulation dot1q
 switchport mode trunk
exit
interface Ethernet0/1
 switchport trunk allowed vlan 10-12
 switchport trunk encapsulation dot1q
 switchport mode trunk
exit
interface Ethernet0/2
 switchport trunk allowed vlan 10-12
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active
exit
interface Ethernet0/3
 switchport trunk allowed vlan 10-12
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active
exit
interface Ethernet1/0
 switchport trunk allowed vlan 10-12
 switchport trunk encapsulation dot1q
 switchport mode trunk
exit
interface Vlan10
 description Management
 ip address 172.16.0.4 255.255.255.0
 ipv6 address FE80::4 link-local
 ipv6 address AC10:FFFF:0:10A1::4/64
no shutdown
exit
ip default-gateway 172.16.0.1
````
#### Пример настройки VPC1.
````
ip 192.168.0.4/24 255.255.255.0
````
### Часть 5: Итоговая схема.
![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab04/lab04.png)

