# DHCP

##  Задание:

Настроить DHCPv4

Настроить DHCPv6

##  Решение:

##### Лабораторная работа выполнена на базе Cisco Packet Tracer.

# Настроить DHCPv4.

#### Таблица адресов:
| Device	| Interface	| IP Address |	Subnet Mask	| Default Gateway |
| ------	| --------	| ---------- |	-----------	| --------------- |
| R1	| G0/0/0	| 10.0.0.1	| 255.255.255.252	| N/A| 
| R1	| G0/0/1	| N/A	| N/A	| N/A| 
| R1	| G0/0/1.100	| 192.168.1.1 | 255.255.255.192 | N/A| 
| R1	| G0/0/1.200	| 192.168.1.65 | 255.255.255.224 | N/A| 
| R1	| G0/0/1.1000	| N/A	| N/A	| N/A| 
| R2	| G0/0/0	| 10.0.0.2	| 255.255.255.252	| N/A| 
| R2	| G0/0/1	|	192.168.1.97 | 255.255.255.240 | N/A| 
| S1	| VLAN 200	| 192.168.1.2 | 255.255.255.192 | 192.168.1.1 | 
| S2	| VLAN 1	| 192.168.1.66 | 255.255.255.224 | 192.168.1.65 | 
| PC-A	| NIC	| DHCP	| DHCP	| DHCP| 
| PC-B	| NIC	| DHCP	| DHCP	| DHCP| 

#### Таблица VLAN:
| VLAN	| Name	| Interface Assigned |
| -----	| -----	| ------------------ |
| 1	| N/A	| S2: F0/18 |
| 100	| Clients	| S1: F0/6 |
| 200	| Management	| S1: VLAN 200  |
| 999	| Parking_Lot	| S1: F0/1-4, F0/7-24, G0/1-2 |
| 1000	| Native	| N/A |

#### Конфигурации оборудования
[Итоговые конфиги оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab03/Configs).

#### Цели:

Часть 1: Строим сеть и настраиваем базовые настройки.

Часть 2: Конфигурируем и проверяем два DHCPv4 сервера на R1.

Часть 3: Конфигурируем и проверяем DHCP Relay на R2.

### Часть 1: Строим сеть и настраиваем базовые настройки.

#### Шаг 1: Распределение адресов.

192.168.1.0/26 - Subnet A (the client VLAN at R1).

192.168.1.64/27 - Subnet B (the management VLAN at R1).

192.168.1.96/28 - Subnet C (the client network at R2).

#### Шаг 2: Схема сети.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab03/scheme%201.png)

#### Шаг 3: Базовая настройка роутеров и коммутаторов.
```
hostname S1/S2/R1/R2
no ip domain-lookup
enable secret class
line con 0
login
password cisco
exit
line vty 0 4
login
password cisco
exit
service password-encryption
clock timezone msk 3
banner motd ^C Accessing the device that unauthorized access is prohibited ^C
```
#### Шаг 4: R1 - Настроим g0/0/0 и включим g0/0/1, настроим саб-интерфейсы согласно адресной таблице и пропишем ip route.
```
int g0/0/0
ip address 10.0.0.1 255.255.255.252
no shutdown
exit
int g0/0/1
no shutdown
exit
int g0/0/1.100
description vlan 100
encapsulation dot1Q 100
ip address 192.168.1.1 255.255.255.192
exit
int g0/0/1.200
description vlan 200
encapsulation dot1Q 200
ip address 192.168.1.65 255.255.255.224
exit
int g0/0/1.1000
description vlan 1000
encapsulation dot1Q 1000
exit
ip route 0.0.0.0 0.0.0.0 10.0.0.2
```
#### Шаг 5: R2 - Настроим g0/0/0, g0/0/1 и пропишем ip route.
```
int g0/0/1
ip address 192.168.1.97 255.255.255.240
no shutdown
exit
int g0/0/0
ip address 10.0.0.2 255.255.255.252
no shutdown
exit
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```
#### Шаг 6: Проверяем статический маршрут между R1 и R2.
```
R1#ping 10.0.0.2 

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms
```
#### Шаг 7: S1 - Создаем VLAN-ы, добавляем шлюз по умолчанию и конфигурируем порты.
```
vlan 100
name Clients
exit
vlan 200
name Management
exit
vlan 999
name Parking_Lot
exit
vlan 1000
name Native
exit
int vlan 200
description Management
ip address 192.168.1.2 255.255.255.192
exit
ip default-gateway 192.168.1.1
interface range f0/1-24
switchport mode access
switchport access vlan 999
shutdown
exit
interface range g0/1-2
switchport mode access
switchport access vlan 999
shutdown
exit
default interface f0/5
default interface f0/6
int f0/6
switchport mode access
switchport access vlan 100
exit
int f0/5
switchport trunk native vlan 1000
switchport trunk allowed vlan 100,200,1000
switchport mode trunk
```
#### Шаг 8: S2 - Конфигурируем порты и добавляем шлюз по умолчанию.
```
interface range f0/1-24
shutdown
exit
interface range g0/1-2
shutdown
default interface f0/5
default interface f0/18
int f0/5
switchport mode access
exit
int f0/18
switchport mode access
exit
int vlan 1
description Management
ip address 192.168.1.66 255.255.255.224
no shutdown
exit
ip default-gateway 192.168.1.65
```

### Часть 2: Конфигурируем и проверяем два DHCPv4 сервера на R1.

#### Шаг 1: Конфигурируем два DHCPv4 сервера на R1.
```
ip dhcp excluded-address 192.168.1.1 192.168.1.5
ip dhcp pool DHCP_192.168.1.0/26 
network 192.168.1.0 255.255.255.192
default-router 192.168.1.1
domain-name ccna-lab.com
lease 7 12 30
exit
ip dhcp excluded-address 192.168.1.97
ip dhcp pool R2_Client_LAN 
network 192.168.1.96 255.255.255.240
default-router 192.168.1.97
domain-name ccna-lab.com
lease 7 12 30
```
#### Шаг2: Проверяем выдачу IP адреса на PC-A.
![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab03/PC-A.png)

#### Шаг3: Пингуем шлюз.
![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab03/PC-A-ping%20192.168.1.1.png)

### Часть 3: Конфигурируем и проверяем DHCP relay на R2.

#### Шаг1: Добавляем ip helper-address на g0/0/1.
```
int g0/0/1
ip helper-address 10.0.0.1
```
#### Шаг2: Проверяем выдачу IP адреса на PC-В.
![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab03/PС-B.png)

#### Шаг3: Пингуем шлюз.
![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab03/PС-B%20-%20ping%20192.168.1.97.png)

# Настроить DHCPv6.

#### Таблица адресов:

| Device	| Interface	| IPv6 Address |
| ------	| --------	| ------------ |
| R1	| G0/0/0	| 2001:db8:acad:2::1/64	|
| R1	| G0/0/0	| fe80::1	|
| R1	| G0/0/1	| 2001:db8:acad:1::1/64	|
| R1	| G0/0/1	| fe80::1	|
| R2	| G0/0/0	| 2001:db8:acad:2::2/64	|
| R2	| G0/0/0	| fe80::2	|
| R2	| G0/0/1	| 2001:db8:acad:3::1/64	|
| R2	| G0/0/1	| fe80::1	|
| PC-A	| NIC	| DHCP	|
| PC-B	| NIC	| DHCP	|

#### Конфигурации оборудования
[Итоговые конфиги оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab03/Configs).

#### Цели:
Часть 1: Построение сети и настройка основных параметров устройств.
Часть 2: Проверка назначения адреса SLAAC от R1.
Часть 3: Настройка и проверка Stateless DHCPv6 сервера на R1.
Часть 4. Настройка и проверка Stateful DHCPv6 сервера на R1.
Часть 5: Настройка и проверка DHCPv6 Relay на R2.

### Часть 1: Построение сети и настройка основных параметров устройств.
### Схема сети.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab03/scheme%201.png)

#### Шаг 1: Базовая настройка оборудования
```
hostname S1/S2/R1/R2
no ip domain-lookup
enable secret class
line con 0
login
password cisco
exit
line vty 0 4
login
password cisco
exit
service password-encryption
clock timezone msk 3
banner motd ^C Accessing the device that unauthorized access is prohibited ^C
```
#### Шаг 2: R1 - Добавляем IPv6 на интерфейс g0/0/0 и g0/0/1, добавляем маршрут по умолчанию и включаем ipv6 unicast-routing
```
int g0/0/0
ipv6 address 2001:db8:acad:2::1/64
ipv6 address fe80::1 link-local
no shutdown
exit
int g0/0/1
ipv6 address 2001:db8:acad:1::1/64
ipv6 address fe80::1 link-local
no shutdown
exit
ipv6 route ::/0 2001:db8:acad:2::2
ipv6 unicast-routing
```
#### Шаг 3: R2 - Добавляем IPv6 на интерфейс g0/0/0 и g0/0/1, добавляем маршрут по умолчанию и включаем ipv6 unicast-routing
```
int g0/0/0
ipv6 address 2001:db8:acad:2::2/64
ipv6 address fe80::2 link-local
no shutdown
exit
int g0/0/1
ipv6 address 2001:db8:acad:3::1/64
ipv6 address fe80::1 link-local
no shutdown
exit
ipv6 route ::/0 2001:db8:acad:2::1
ipv6 unicast-routing
```
#### Шаг 4: Проверяем доступность g0/0/1 на R2 c R1.
```
R1#ping ipv6 2001:db8:acad:3::1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:db8:acad:3::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms
```
### Часть 2: Проверка назначения адреса SLAAC от R1.

### Шаг 1: Проверяем, что на PC-A включен автоматический IPv6 и смотрим ipconfig
```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2D0:BAFF:FE4B:5749
   IPv6 Address....................: 2001:DB8:ACAD:1:2D0:BAFF:FE4B:5749
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
```
### Часть 3 Настройка и проверка Stateless DHCPv6 сервера на R1.

#### Шаг 1: Текущая сетевая конфигурация PC-A.
```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 00D0.BA4B.5749
   Link-local IPv6 Address.........: FE80::2D0:BAFF:FE4B:5749
   IPv6 Address....................: 2001:DB8:ACAD:1:2D0:BAFF:FE4B:5749
   Autoconfiguration IP Address....: 169.254.87.73
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-C5-32-E2-B3-00-D0-BA-4B-57-49
   DNS Servers.....................: ::
                                     0.0.0.0
```
#### Шаг 2: Настройка R1 для предоставления stateless DHCPv6 для PC-А.
```
ipv6 dhcp pool R1-STATELESS
dns-server 2001:db8:acad::254
domain-name STATELESS.com
exit
interface g0/0/1
ipv6 nd other-config-flag
ipv6 dhcp server R1-STATELESS
exit
```
#### Шаг 3: Смотрим ipconfig на PC-A, видим появление настроек с DHCP сервера.
```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: STATELESS.com 
   Physical Address................: 00D0.BA4B.5749
   Link-local IPv6 Address.........: FE80::2D0:BAFF:FE4B:5749
   IPv6 Address....................: 2001:DB8:ACAD:1:2D0:BAFF:FE4B:5749
   Autoconfiguration IP Address....: 169.254.87.73
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 1825619641
   DHCPv6 Client DUID..............: 00-01-00-01-C5-32-E2-B3-00-D0-BA-4B-57-49
   DNS Servers.....................: 2001:DB8:ACAD::254
                                     0.0.0.0
```
#### Шаг 4: Пингуем c PC-A G0/0/1 на R2.
```
C:\>ping 2001:db8:acad:3::1 

Pinging 2001:db8:acad:3::1 with 32 bytes of data:

Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time=3ms TTL=254

Ping statistics for 2001:DB8:ACAD:3::1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 3ms, Average = 0ms
```
### Часть 4: Настройка и проверка Stateful DHCPv6 сервера на R1.
```
ipv6 dhcp pool R2-STATEFUL
address prefix 2001:db8:acad:3:aaa::/80
dns-server 2001:db8:acad::254
domain-name STATEFUL.com
exit
interface g0/0/0
ipv6 dhcp server R2-STATEFUL
```
### Часть 5: Настройка и проверка DHCPv6 Relay на R2.

#### Шаг 1: Проверяем, что на PC-B включен автоматический IPv6 и смотрим ipconfig/all.
```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0001.96E2.030D
   Link-local IPv6 Address.........: FE80::201:96FF:FEE2:30D
   IPv6 Address....................: 2001:DB8:ACAD:3:201:96FF:FEE2:30D
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-BC-10-80-50-00-01-96-E2-03-0D
   DNS Servers.....................: ::
                                     0.0.0.0
```
#### Шаг 2: Конфигурируем R2 в качестве DHCP relay agent для локальной сети на G0/0/1.
```
interface g0/0/1
ipv6 nd managed-config-flag
ipv6 dhcp relay destination 2001:db8:acad:2::1 g0/0/0
exit
```
#### Шаг 3: Попытка получения адреса IPv6 от DHCPv6 на PC-B.
```
В Cisco packet tracer на всех маршрутизаторах нет ipv6 dhcp relay. А в EVE-NG нужно разбираться. Поэтому проверить нет возможности. Но должно все заработать.
```
