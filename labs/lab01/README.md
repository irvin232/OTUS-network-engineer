# VLAN и маршрутизация между VLAN

###  Задание:
Добавление сетей VLAN и назначение портов

###  Решение:

##### Лабораторная работа выполнена на базе Cisco Packet Tracer.

##### Схема сети:

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab01/network%20topology.png)

##### Основные шаги настройки оборудования:
- Делаем базовые настройки на оборудовании;
- Создаем vlan'ы и интерфейсы в vlan'ах на свичах;
- Конфигурируем транки на свичах;
- Конфигурируем роутинг между vlan'ами на роутере;
- [Итоговые конфиги оборудования](https://github.com/irvin232/OTUS-network-engineer/tree/master/labs/lab01/Configs).

##### Проверяем, что маршрутизация между vlan'ами работает.
- Назначаем PCA IP-адрес 192.168.3.3
- Назначаем PCB IP-адрес 192.168.4.3
- Пингуем с PCA шлюз 192.168.3.1, PCB 192.168.4.3 и S2 192.168.3.12
![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab01/Ping%20from%20PCA.png)

- Делаем трассировку с PCB до PCA

![](https://github.com/irvin232/OTUS-network-engineer/blob/master/labs/lab01/Tracert%20from%20PCB.png)

###  END
