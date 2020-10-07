# Избыточность локальных сетей. STP

##  Задание:
Настройка STP

Цель: Создание сети и настройка основных параметров устройства

Выбор корневого моста

Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

В этой лабораторной работе вам предстоит настроить протокол Cisco STP, являющийся протоколом FHRP.

В части 1 вам предстоит настроить топологию сети и основные параметры маршрутизаторов.

В части 2 Для каждого экземпляра протокола spanning-tree (коммутируемая сеть LAN или широковещательный домен) существует коммутатор, выделенный в качестве корневого моста.

В части 3 Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов.

В части 4 вам предстоит активировать избыточные пути до каждого из коммутаторов, чтобы просмотреть, каким образом протокол STP выбирает порт с учетом приоритета портов.

##  Решение:

##### Лабораторная работа выполнена на базе Cisco Packet Tracer.

##### Схема сети:

![]()

#### Часть 1:	Создание сети и настройка основных параметров устройства

В части 1 настраиваем топологию сети и основные параметры маршрутизаторов.
#### Шаг 1:	Создаем сеть согласно топологии.
Подключаем устройства, как показано в топологии, и подсоединяем необходимые кабели.
#### Шаг 2:	Выполняем инициализацию и перезагрузку коммутаторов.
#### Шаг 3:	Настраиваем базовые параметры каждого коммутатора.
a.	Отключаем поиск DNS.
```
no ip domain-lookup
```
b.	Присваиваем имена устройствам в соответствии с топологией.
```
hostname S1/S2/S3
```
c.	Назначаем **class** в качестве зашифрованного пароля доступа к привилегированному режиму.
```
enable secret class
```
d.	Назначаем **cisco** в качестве паролей консоли и VTY и активируем вход для консоли и VTY каналов.
```
line con 0
login
password cisco
exit
line vty 0 4
login
password cisco
exit
```
e.	Настроим **logging synchronous** для консольного канала.
```
line con 0
login synchronous
exit
```
f.	Настроим баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.
```
banner login ^C Accessing the device that unauthorized access is prohibited ^C
```
g.	Задаем IP-адреса, указанные в таблице адресации для VLAN 1 на всех коммутаторах.
```
int vlan 1
ip address 192.168.1.1/2/3 255.255.255.0
no shutdown
exit
```
h.	Скопируем текущую конфигурацию в файл загрузочной конфигурации.
```
wr
```
#### Шаг 4:	Проверяем связь. Проверяем способность компьютеров обмениваться эхо-запросами.

эхо-запрос от коммутатора S1 на коммутатор S2
```
S1#ping 192.168.1.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/3 ms
```
эхо-запрос от коммутатора S1 на коммутатор S3
```
S1#ping 192.168.1.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms
```
эхо-запрос от коммутатора S2 на коммутатор S3
```
S2#ping 192.168.1.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```

#### Часть 2:	Определение корневого моста

Для каждого экземпляра протокола spanning-tree (коммутируемая сеть LAN или широковещательный домен) существует коммутатор, выделенный в качестве корневого моста. Корневой мост служит точкой привязки для всех расчётов протокола spanning-tree, позволяя определить избыточные пути, которые следует заблокировать.
Процесс выбора определяет, какой из коммутаторов станет корневым мостом. Коммутатор с наименьшим значением идентификатора моста (BID) становится корневым мостом. Идентификатор BID состоит из значения приоритета моста, расширенного идентификатора системы и MAC-адреса коммутатора. Значение приоритета может находиться в диапазоне от 0 до 65535 с шагом 4096. По умолчанию используется значение 32768.

#### Шаг 1:	Отключаем все порты на коммутаторах.
Отправляем все порты в down командой ***shutdown***

#### Шаг 2:	Настраиваем подключенные порты в качестве транковых.
```
int range f0/1-4
switchport mode trunk
exit
```
#### Шаг 3:	Включаем порты F0/2 и F0/4 на всех коммутаторах.
Включаем нужные порты командой ***no shutdown***

#### Шаг 4:	Отображаем данные протокола spanning-tree.

```
S1#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A7B.3A44
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.FFA7.B486
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/4            Root FWD 19        128.4    P2p
```
```
S2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A7B.3A44
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0090.216B.0DE9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Root FWD 19        128.4    P2p
Fa0/2            Desg FWD 19        128.2    P2p
```
```
S3#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A7B.3A44
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0006.2A7B.3A44
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p
```

Примечание. Режим STP по умолчанию на коммутаторе 2960 — протокол STP для каждой сети VLAN (PVST).

В схему ниже запишем роль и состояние (Sts) активных портов на каждом коммутаторе в топологии.

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab02/scheme%202.png)
 
С учетом выходных данных, поступающих с коммутаторов, отвечаем на следующие вопросы:

Какой коммутатор является корневым мостом? 
```
S3
```
Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?
```
mac-адрес младше всех
```
Какие порты на коммутаторе являются корневыми портами?
```
На S1 и S2 порты f0/4 корневые
```
Какие порты на коммутаторе являются назначенными портами? 
```
На S2 - f0/2, на S3 - f0/2 и f0/4
```

Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? 
```
На S1 - f0/2
```
Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?
```
При одинаковых BID 32769, mac-адрес коммутатора S1 старше S2, а стоимость маршрута к корневому мосту через S2 больше.
```

#### Часть 3:	Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

Алгоритм протокола spanning-tree (STA) использует корневой мост как точку привязки, после чего определяет, какие порты будут заблокированы, исходя из стоимости пути. Порт с более низкой стоимостью пути является предпочтительным. Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Наиболее низкие значения являются предпочтительными. В части 3 вам предстоит изменить стоимость порта, чтобы определить, какой порт будет заблокирован протоколом spanning-tree.

#### Шаг 1:	Определяем коммутатор с заблокированным портом.

При текущей конфигурации только один коммутатор может содержать заблокированный протоколом STP порт. Выполняем команду ***show spanning-tree*** на обоих коммутаторах некорневого моста. В примере ниже протокол spanning-tree блокирует порт F0/2 на коммутаторе с самым высоким идентификатором BID (S1).
```
S1#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A7B.3A44
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.FFA7.B486
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/4            Root FWD 19        128.4    P2p
```
```
S2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A7B.3A44
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0090.216B.0DE9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Root FWD 19        128.4    P2p
Fa0/2            Desg FWD 19        128.2    P2p
```

#### Шаг 2:	Изменяем стоимость порта.

Помимо заблокированного порта, единственным активным портом на этом коммутаторе является порт, выделенный в качестве порта корневого моста. Уменьшаем стоимость этого порта корневого моста до 18, выполнив команду ***spanning-tree vlan 1 cost 18*** режима конфигурации интерфейса.
```
S1(config)# interface f0/4
S1(config-if)# spanning-tree vlan 1 cost 18
```
#### Шаг 3:	Просмотрим изменения протокола spanning-tree.

Повторно выполните команду ***show spanning-tree*** на обоих коммутаторах некорневого моста. Обратите внимание, что ранее заблокированный порт (S1 – F0/2) теперь является назначенным портом, и протокол spanning-tree теперь блокирует порт на другом коммутаторе некорневого моста (S2 – F0/2).
```
S1#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A7B.3A44
             Cost        18
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.FFA7.B486
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Root FWD 18        128.4    P2p
Fa0/2            Desg FWD 19        128.2    P2p
```
```
S2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A7B.3A44
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0090.216B.0DE9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Root FWD 19        128.4    P2p
Fa0/2            Altn BLK 19        128.2    P2p
```
Почему протокол spanning-tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, который был назначенным портом на другом коммутаторе?
```
Потому что состояния портов определяются по стоимости пути к корневому мосту.
```
Шаг 4:	Удаляем изменения стоимости порта.

a.	Выполняем команду ***no spanning-tree vlan 1 cost 18*** режима конфигурации интерфейса, чтобы удалить запись стоимости, созданную ранее.
```
S1(config)# interface f0/4
S1(config-if)# no spanning-tree vlan 1 cost 18
```
b.	Повторно выполняем команду ***show spanning-tree***, чтобы подтвердить, что протокол STP сбросил порт на коммутаторе некорневого моста, вернув исходные настройки порта. Протоколу STP требуется примерно 30 секунд, чтобы завершить процесс перевода порта.
```
S1#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A7B.3A44
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.FFA7.B486
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Root FWD 19        128.4    P2p
Fa0/2            Altn BLK 19        128.2    P2p
```

#### Часть 4:	Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Значение приоритета по умолчанию — 128. STP объединяет приоритет порта с номером порта, чтобы разорвать связи. Наиболее низкие значения являются предпочтительными. В части 4 нам предстоит активировать избыточные пути до каждого из коммутаторов, чтобы просмотреть, каким образом протокол STP выбирает порт с учетом приоритета портов.

a.	Включаем порты F0/1 и F0/3 на всех коммутаторах.
```
conf t
int f0/1
no shutdown
exit
int f0/3
no shutdown
exit
```

b.	Ждем 30 секунд, чтобы протокол STP завершил процесс перевода порта, после чего выполняем команду ***show spanning-tree*** на коммутаторах некорневого моста. Обращаем внимание, что порт корневого моста переместился на порт с меньшим номером, связанный с коммутатором корневого моста, и заблокировал предыдущий порт корневого моста.
```
S1#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A7B.3A44
             Cost        19
             Port        3(FastEthernet0/3)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.FFA7.B486
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/3            Root FWD 19        128.3    P2p
Fa0/4            Altn BLK 19        128.4    P2p
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/1            Altn BLK 19        128.1    P2p
```
```
S2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A7B.3A44
             Cost        19
             Port        3(FastEthernet0/3)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0090.216B.0DE9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Altn BLK 19        128.4    P2p
Fa0/1            Desg FWD 19        128.1    P2p
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/3            Root FWD 19        128.3    P2p
```
Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста?
```
S1- f0/3, S2 - f0/3
```
Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах?
```
Наименьшая стоимость пути
```

#### Вопросы для повторения
1.	Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта?
```
Bridge ID  Priority.
```
2.	Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта?
```
Стоимость (Cost) маршрута.
```
3.	Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта?
```
Номер порта на коммутаторе.
```
