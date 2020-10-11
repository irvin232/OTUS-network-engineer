# DHCP

##  Задание:
Настроить DHCPv4
Настроить DHCPv6

##  Решение:

##### Лабораторная работа выполнена на базе Cisco Packet Tracer.

### Настроить DHCPv4

##### Схема сети:

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab03/scheme%201.png)

#### Таблица адресов
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

#### Таблица VLAN
| VLAN	| Name	| Interface Assigned |
| -----	| -----	| ------------------ |
| 1	| N/A	| S2: F0/18 |
| 100	| Clients	| S1: F0/6 |
| 200	| Management	| S1: VLAN 200  |
| 999	| Parking_Lot	| S1: F0/1-4, F0/7-24, G0/1-2 |
| 1000	| Native	| N/A |

#### Цели

Часть 1: Строим сеть и настраиваем базовые настройки

Часть 2: Конфигурируем и проверяем два DHCPv4 сервера на R1

Часть 3: Конфигурируем и проверяем DHCP Relay на R2

#### Часть 1: Строим сеть и настраиваем базовые настройки

Шаг 1: Распределение адресов

192.168.1.0/26 - Subnet A (the client VLAN at R1).

192.168.1.64/27 - Subnet B (the management VLAN at R1).

192.168.1.96/28 - Subnet C (the client network at R2).

Шаг 2: Схема сети


Шаг 3: Базовая настройка роутеров и коммутаторов:
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
wr
```

Шаг 4: R1 - Настроим g0/0/0 и включим g0/0/1, настроим саб-интерфейсы согласно адресной таблице и пропишем ip route.
```
conf t
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
wr
```
Шаг 5: R2 - Настроим g0/0/0, g0/0/1 и пропишем ip route.
```
conf t
int g0/0/1
ip address 192.168.1.97 255.255.255.240
no shutdown
exit
int g0/0/0
ip address 10.0.0.2 255.255.255.252
no shutdown
exit
ip route 0.0.0.0 0.0.0.0 10.0.0.1
wr
```
Шаг 6: Проверяем статический маршрут между R1 и R2
```
R1#ping 10.0.0.2 

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms
```

Шаг 7: S1 - Создаем VLAN-ы, добавляем шлюз по умолчанию и конфигурируем порты.
```
conf t
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
Шаг 8: S2 - Конфигурируем порты
```
conf t
interface range f0/1-24
shutdown
exit
interface range g0/1-2
shutdown
default interface f0/5
default interface f0/18
int f0/5
switchport trunk native vlan 1000
switchport trunk allowed vlan 100,200,1000
switchport mode trunk
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
