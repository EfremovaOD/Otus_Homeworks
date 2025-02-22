# Лабораторная работа. Доступ к сетевым устройствам по протоколу SSH
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/c625aadd110538986bfd1673cc7b7894517ac375/photo/Homework5/Topology.png)



## Таблица адресации 

| Устройство  | Интерфейс  | IP-адрес | Маска подсети | Шлюз по умолчанию |
| :------------ |:---------------:| -----:| -----:| -----:|
| R1      | G0/0/1.10   | 192.168.10.1  | 255.255.255.0 | - |
| R1      | G0/0/1.20   | 192.168.20.1  | 255.255.255.0 | - |
| R1      | G0/0/1.30   | 192.168.30.1  | 255.255.255.0 | - |
| R1      | G0/0/1.1000 |       -       |       -       | - |
| S1      | VLAN 10 | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| S2      | VLAN 10 | 192.168.10.12 | 255.255.255.0 | 192.168.10.1 |
| PC-A    | NIC     | 192.168.20.3  | 255.255.255.0 | 192.168.20.1 |
| PC-B    | NIC     | 192.168.30.3  | 255.255.255.0 | 192.168.30.1 |

## Таблица VLAN

| VLAN  | Имя | Назначенный интерфейс |
| :------------ |:---------------:| -----:|
| 10      | Управление    | S1: VLAN 10  |
| 10      | Управление    | S2: VLAN 10  | 
| 20      | Sales         | S1: F0/6     |
| 30      | Operations    | S2: F0/18    |
| 999     | Parking_Lot   | S1: F0/2-4, F0/7-24, G0/1-2   |
| 999     | Parking_Lot   | S2: F0/2-17, F0/19-24, G0/1-2 |
| 1000    | Собственная   | - |

## Задачи:

### Часть 1. Создание сети и настройка основных параметров устройства.

### Часть 2. Создание сетей VLAN и назначение портов коммутатора.

### Часть 3. Настройка транка 802.1Q между коммутаторами.

### Часть 4. Настройка маршрутизации между сетями VLAN.

### Часть 5. Проверка, что маршрутизация между VLAN работает.

## Решение:

### Часть 1. Создание сети и настройка основных параметров устройства.

#### Шаг 1. Создаем сеть согласно топологии.

#### Шаг 2. Настройте базовые параметры для маршрутизатора:

    Router>en
    Router#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    Router(config)#no ip domain-lookup
    Router(config)#enable secret class
    Router(config)#service password-
    Router(config)#service password-encryption 
    Router(config)#
    Router(config)#hostname R1
    R1(config)#banner motd #
    Enter TEXT message.  End with the character '#'.
        *************************************************************************************ATTENTION*****************************************************************************#
    R1(config)#exit
    R1#clock set 21:00:00 feb 22 2025
    R1#conf t
    R1(config)#line console 0
    R1(config-line)#password cisco
    R1(config-line)#login
    R1(config-line)#exit
    R1(config)#line vty 0 4
    R1(config-line)#password cisco
    R1(config-line)#login
    R1(config-line)#exit
    R1(config)#int g0/0/1
    R1(config-if)#no shutdown
    R1(config-if)#end
    R1#copy running-config startup-config

#### Шаг 3. Настройте базовые параметры каждого коммутатора:
   1. S1:

    Switch>
    Switch>en
    Switch#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    Switch(config)#no ip domain-lookup
    Switch(config)#
    Switch(config)#enable secret class
    Switch(config)#
    Switch(config)#service password
    Switch(config)#service password-encryption 
    Switch(config)#
    Switch(config)#hostname S1
    S1(config)#
    S1(config)#banner motd #
    Enter TEXT message.  End with the character '#'.
    *******************ATTENTION*****************************#

    S1(config)#exit
    S1#clock set 21:09:00 feb 22 2025
    S1#conf t
    S1(config)#line console 0
    S1(config-line)#
    S1(config-line)#password cisco
    S1(config-line)#login
    S1(config-line)#exit
    S1(config)#line vty 0 4
    S1(config-line)#password cisco
    S1(config-line)#login
    S1(config-line)#
    S1(config-line)#exit
    S1(config)#int vlan 1
    S1(config-if)#no shutdo
    S1(config-if)#no shutdown 
    
    S1(config-if)#
    %LINK-5-CHANGED: Interface Vlan1, changed state to up
    
    %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
    
    S1(config-if)#end
    S1#copy running-config startup-config 
    
  2. Аналогичные настройки производим на S2.

#### Шаг 4. Настройте узлы ПК:

1. PC-A:

![PC-A_IP](https://github.com/EfremovaOD/Otus_Homeworks/blob/c625aadd110538986bfd1673cc7b7894517ac375/photo/Homework5/Topology.png)

2. PC-B:

![PC-B_IP](https://github.com/EfremovaOD/Otus_Homeworks/blob/c625aadd110538986bfd1673cc7b7894517ac375/photo/Homework5/Topology.png)


### Часть 2. Создание сетей VLAN и назначение портов коммутатора.

#### Шаг 1. Создайте сети VLAN на коммутаторах:

  1. S1:

    S1(config)#vlan 10
    S1(config-vlan)# name Management
    S1(config-vlan)#
    S1(config-vlan)#exit
    S1(config)#vlan 20
    S1(config-vlan)#name Sales
    S1(config-vlan)#exit
    S1(config)#vlan 30
    S1(config-vlan)#name Operations
    S1(config-vlan)#
    S1(config-vlan)#exit
    S1(config)#
    S1(config)#vlan 999
    S1(config-vlan)#name Parking_Lot
    S1(config-vlan)#
    S1(config-vlan)#exit
    S1(config)#
    S1(config)#
    S1(config)#vlan 1000
    S1(config-vlan)#
    S1(config-vlan)#name NATIVE
    S1(config-vlan)#

  2. Аналогичные настройки производим на S2.








