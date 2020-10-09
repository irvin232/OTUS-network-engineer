# DHCP

##  Задание:
Настроить DHCPv4
Настроить DHCPv6

##  Решение:

##### Лабораторная работа выполнена на базе Cisco Packet Tracer.

##### Схема сети:


#### Addressing Table
| Device	| Interface	| IP Address |	Subnet Mask	| Default Gateway |
| ------	| --------	| ---------- |	-----------	| --------------- |
| R1	| G0/0/0	| 10.0.0.1	| 255.255.255.252	| N/A| 
| R1	| G0/0/1	| N/A	| N/A	| N/A| 
| R1	| G0/0/1.100	| | | | N/A| 
| R1	| G0/0/1.200	| | | | N/A| 
| R1	| G0/0/1.1000	| N/A	| N/A	| N/A| 
| R2	| G0/0/0	| 10.0.0.2	| 255.255.255.252	| N/A| 
| R2	| G0/0/1	| blank	| blank	| N/A| 
| S1	| VLAN 200	| | | | 
| S2	| VLAN 1	| | | | 
| PC-A	| NIC	| DHCP	| DHCP	| DHCP| 
| PC-B	| NIC	| DHCP	| DHCP	| DHCP| 

VLAN Table
VLAN	Name	Interface Assigned
1	N/A	S2: F0/18
100	Clients	S1: F0/6
200	Management	S1: VLAN 200 
999	Parking_Lot	S1: F0/1-4, F0/7-24, G0/1-2
1000	Native	N/A
Objectives
Part 1: Build the Network and Configure Basic Device Settings
Part 2: Configure and verify two DHCPv4 Servers on R1
Part 3: Configure and verify a DHCP Relay on R2
