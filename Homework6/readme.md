# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/2d77218e6e3a00dc0b4a888e897a9c2e3183eaeb/photo/homework6/Topology.png)



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
| 10      | Manaagement       | S1: VLAN 10  |
| 10      | Management        | S2: VLAN 10  | 
| 20      | Sales             | S1: F0/6     |
| 30      | Operations        | S2: F0/18    |
| 999     | Parking_Lot       | S1: F0/2-4, F0/7-24, G0/1-2   |
| 999     | Parking_Lot       | S2: F0/2-17, F0/19-24, G0/1-2 |
| 1000    | NATIVE            | - |

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
    R1(config-line)#end
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
    S1(config-line)#pass cisco
    S1(config-line)#login
    S1(config-line)#exit
    S1(config)#line vty 0 4
    S1(config-line)#pass cisco
    S1(config-line)#login
    S1(config-line)#
    S1(config-line)#end
    S1#copy running-config startup-config 
    
  2. Аналогичные настройки производим на S2.

#### Шаг 4. Настройте узлы ПК:

1. PC-A:

![PC-A_IP](https://github.com/EfremovaOD/Otus_Homeworks/blob/2d77218e6e3a00dc0b4a888e897a9c2e3183eaeb/photo/homework6/PC-A_IP.png)

2. PC-B:

![PC-B_IP](https://github.com/EfremovaOD/Otus_Homeworks/blob/2d77218e6e3a00dc0b4a888e897a9c2e3183eaeb/photo/homework6/PC-B_IP.png)


### Часть 2. Создание сетей VLAN и назначение портов коммутатора.

#### Шаг 1. Создайте сети VLAN на коммутаторах:

  1. S1:

    S1(config)#vlan 10
    S1(config-vlan)# name Management
    S1(config-vlan)#
    S1(config-vlan)#vlan 20
    S1(config-vlan)#name Sales
    S1(config-vlan)#vlan 30
    S1(config-vlan)#name Operations
    S1(config-vlan)#
    S1(config-vlan)#vlan 999
    S1(config-vlan)#name Parking_Lot
    S1(config-vlan)#
    S1(config-vlan)#vlan 1000
    S1(config-vlan)#
    S1(config-vlan)#name NATIVE
    S1(config-vlan)#exit
    S1(config)#int vlan 10
    S1(config-if)#
    %LINK-5-CHANGED: Interface Vlan10, changed state to up
    
    S1(config-if)#
    S1(config-if)#ip add 192.168.10.11 255.255.255.0
    S1(config-if)#no sh
    S1(config-if)#exit
    S1(config)#ip def
    S1(config)#ip default-gateway 192.168.10.1
    S1(config)#int range F0/2-4,F0/7-24,G0/1-2
    S1(config-if-range)#switchport mode ac
    S1(config-if-range)#switchport mode access
    S1(config-if-range)#switchport access vl 999
    S1(config-if-range)#shutdown

  2. Аналогичные настройки производим на S2:

    S2(config)#vlan 10
    S2(config-vlan)#name Management
    S2(config-vlan)#vlan 20
    S2(config-vlan)#name Sales
    S2(config-vlan)#vlan 30
    S2(config-vlan)#name Operations
    S2(config-vlan)#vlan 999
    S2(config-vlan)#name Parking_Lot
    S2(config-vlan)#vlan 1000
    S2(config-vlan)#name Custom
    S2(config-vlan)#exit
    S2(config)#int vlan 10
    S2(config-if)#ip add 192.168.10.12 255.255.255.0
    S2(config-if)#no shutdown
    S2(config-if)#exit
    S2(config)#ip default-gateway 192.168.10.1
    S2(config)#int range F0/2-17,F0/19-24,G0/1-2
    S2(config-if-range)#switchport mode access
    S2(config-if-range)#switchport access vlan 999
    S2(config-if-range)#shutdown   

#### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора:


  1. S1:

    S1(config)#int f0/6
    S1(config-if)#switchport mode access
    S1(config-if)#switchport access vlan 20
    S1(config-if)#no shutdown

  2. S2:

    S2(config)#int f0/18
    S2(config-if)#switchpor mode access
    S2(config-if)#switchport access vlan 30
    S2(config-if)#no shutdown

### Часть 3. Конфигурация магистрального канала стандарта 802.1Q между коммутаторами.

#### Шаг 1. Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2:

  1. S1:

    S1(config)#int f0/1
    S1(config-if)#switchport mode trunk
    S1(config-if)#switchport trunk native vlan 1000
    S1(config-if)#switchport trunk allowed vlan 10,20,30,1000

  2. S2 по аналогии.

#### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.


    S1(config-if)#int f0/5
    S1(config-if)#switchport mode trunk
    S1(config-if)#switchport trunk native vlan 1000
    S1(config-if)#switchport trunk  allowed vlan 10,20,30,1000

### Часть 4. Настройка маршрутизации между сетями VLAN.

#### Шаг 1. Настройте маршрутизатор:

    R1(config)#int g0/0/1
    R1(config-if)#no shutdown
    R1(config-if)#exit
    R1(config)#int g0/0/1.10
    R1(config-subif)#encapsulation dot1Q 10
    R1(config-subif)# ip address 192.168.10.1 255.255.255.0
    R1(config-subif)#int g0/0/1.20
    R1(config-subif)#
    R1(config-subif)#encapsulation dot1Q 20
    R1(config-subif)#ip add 192.168.20.1 255.255.255.0
    R1(config-subif)#
    R1(config-subif)#int g0/0/1.30
    R1(config-subif)#encapsulation dot1Q 30
    R1(config-subif)#
    R1(config-subif)#ip add 192.168.30.1 255.255.255.0
    R1(config-subif)#int g0/0/1.1000
    R1(config-subif)#encapsulation dot1Q 1000 native
    R1(config-subif)#
    R1(config-subif)#description NATIVE
    R1(config-subif)#
    R1(config-subif)#
    R1(config-subif)#int g0/0/1.10
    R1(config-subif)#description Management
    R1(config-subif)#
    R1(config-subif)#int g0/0/1.20
    R1(config-subif)#description Sales
    R1(config-subif)#
    R1(config-subif)#int g0/0/1.30
    R1(config-subif)#description Operations

### Часть 5. Проверьте, работает ли маршрутизация между VLAN

#### Шаг 1. Выполните следующие тесты с PC-A:

  1.	Отправьте эхо-запрос с PC-A на шлюз по умолчанию:

![PC-A_PING](https://github.com/EfremovaOD/Otus_Homeworks/blob/2d77218e6e3a00dc0b4a888e897a9c2e3183eaeb/photo/homework6/PC-A_Ping.png)
     
  2.	Отправьте эхо-запрос с PC-A на PC-B:

![PC-A_to_PC-B](https://github.com/EfremovaOD/Otus_Homeworks/blob/2d77218e6e3a00dc0b4a888e897a9c2e3183eaeb/photo/homework6/PC-A_to_PC-B.png)

  3.	Отправьте команду ping с компьютера PC-A на коммутатор S2:

![PC-A_to_S2](https://github.com/EfremovaOD/Otus_Homeworks/blob/2d77218e6e3a00dc0b4a888e897a9c2e3183eaeb/photo/homework6/PC-A_to_S2.png)

#### Шаг 2. Пройдите следующий тест с PC-B:

1. В окне командной строки на PC-B выполните команду tracert на адрес PC-A:

![PC-B_TR_to_PC-A](https://github.com/EfremovaOD/Otus_Homeworks/blob/2d77218e6e3a00dc0b4a888e897a9c2e3183eaeb/photo/homework6/PC-B_TR.png)


