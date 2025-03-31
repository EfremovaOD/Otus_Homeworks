# Лабораторная работа - Реализация DHCPv4
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/309ebb8d79c0f895ca21e401de403ab883a94876/photo/Homework8/DHCPv4/Topology.PNG)

## Таблица адресации 

| Устройство  | Интерфейс  | IPv6-адрес | Маска подсети | Шлюз по умолчанию |
| :------ |:------------:| --------:| ---------------:| -----:|
| R1      | G0/0/0       | 10.0.0.1 | 255.255.255.252 | -     |
| R1      | G0/0/1       | -        | -               | -     |
| R1      | G0/0/1.100   | **192.168.1.1** | **255.255.255.192** | -     |
| R1      | G0/0/1.200   | **192.168.1.65** | **255.255.255.224** | -     |
| R1      | G0/0/1.1000  | -        | -               | -     |
| R2      | G0/0         | 10.0.0.2 | 255.255.255.252 | -     |
| R2      | G0/0/1       | **192.168.1.97** | **255.255.255.240** | -     |
| S1      | VLAN 200     | **192.168.1.66** | **255.255.255.224** | **192.168.1.65** |
| S2      | VLAN 1       | **192.168.1.2**  | **255.255.255.192** | **192.168.1.1**  |
| PC-A    | NIC          | DHCP     | DHCP            | DHCP  |
| PC-B    | NIC          | DHCP     | DHCP            | DHCP  |

## Таблица адресации VLAN

| Устройство | Интерфейс  | IPv6-адрес |
| :------- |:---------------:| -----:|
| 1        | Нет         | S2: F0/18    |
| 100      | Customers   | S1: F0/6     |
| 200      | Management  | S1: VLAN 200 |
| 999      | Parking_Lot | S1: F0/1-4, F0/7-24, G0/1-2 |
| 1000     | NATIVE      | - |


## Задачи:

### Часть 1. Создание сети и настройка основных параметров устройства.

### Часть 2. Настройка и проверка двух серверов DHCPv4 на R1.

### Часть 3. Настройка и проверка DHCP-ретрансляции на R2.

## Решение:

### Часть 1. Создание сети и настройка основных параметров устройства.

#### Шаг 1.	Создание схемы адресации:

Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:

  1.	Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).
Подсеть A:

    192.168.1.1

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100.

  2.	Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1). 
Подсеть B:

    192.168.1.65

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200. Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию.

  3.	Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).
Подсеть C:

    192.168.1.97

Запишите первый IP-адрес в таблице адресации для R2 G0/0/1.

#### Шаг 2.	Создаем сеть согласно топологии (см.пункт "Топология").

#### Шаг 3.	Произведите базовую настройку маршрутизаторов:

  1. На примере R1:

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
    R1#clock set 12:15:00 mar 28 2025
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

#### Шаг 4.	Настройка маршрутизации между сетями VLAN на маршрутизаторе R1:

  1.	Активируйте интерфейс G0/0/1 на маршрутизаторе:

    R1(config)#int g0/0/1
    R1(config-if)#no shutdown

  2.	Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса:

    R1(config-if)#int g0/0/1.100
    R1(config-subif)#encapsulation dot1Q 100
    R1(config-subif)#ip address 192.168.1.1 255.255.255.192
    R1(config-subif)#int g0/0/1.200
    R1(config-subif)#encapsulation dot1Q 200
    R1(config-subif)#ip addr 192.168.1.65 255.255.255.224
    R1(config-subif)#int g0/0/1.1000
    R1(config-subif)#encapsulation dot1Q 1000 native
    R1(config-subif)#no ip address


#### Шаг 5.	Настройте G0/0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов:

  1.	Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее:

    R2(config)#int g0/0/1
    R2(config-if)#ip address 192.168.1.97 255.255.255.240
    R2(config-if)#no shutdown

  2.	Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации:

  R1:
    
    R1(config-if)#int g0/0/0
    R1(config-if)#ip address 10.0.0.1 255.255.255.252
    R1(config-if)#no shutdown   
   
   R2:   
    
    R2(config-if)#int g0/0/0
    R2(config-if)#ip address 10.0.0.2 255.255.255.252
    R2(config-if)#no shutdown

  3.	Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе:

    R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1

  4.	Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1:

![Ping_R1_to_R2](https://github.com/EfremovaOD/Otus_Homeworks/blob/309ebb8d79c0f895ca21e401de403ab883a94876/photo/Homework8/DHCPv4/Ping_R1_R2.PNG)

  5. Сохраняем текущую конфигурацию.


#### Шаг 6.	Настройте базовые параметры каждого коммутатора:

   1. На примере S1:

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
    S1#clock set 22:32:00 mar 26 2025
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

#### Шаг 7.	Создайте сети VLAN на коммутаторе S1:

  1. S1:

    S1(config)#vlan 100
    S1(config-vlan)#name Clients
    S1(config-vlan)#vlan 200
    S1(config-vlan)#name Management
    S1(config-vlan)#
    S1(config-vlan)#vlan 999
    S1(config-vlan)#name Parking_Lot
    S1(config-vlan)#
    S1(config-vlan)#vlan 1000
    S1(config-vlan)#name NATIVE
    S1(config-vlan)#exit
    S1(config)#int vlan 200
    S1(config-if)#ip address 192.168.1.66 255.255.255.224
    S1(config-if)#no shutdown
    S1(config-if)#exit
    S1(config)#ip default-gateway 192.168.1.65
    S1(config)#int range f0/1-4,f0/7-24,g0/1-2
    S1(config-if-range)#switchport mode access
    S1(config-if-range)#switchport access vlan 999
    S1(config-if-range)#shutdown

  2. Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2:

    S2#conf t
    S2(config)#int vlan 1
    S2(config-if)#ip add 192.168.1.2 255.255.255.192
    S2(config-if)#
    S2(config-if)#no shutdown
    S2(config-if)#exit
    S2(config)#
    S2(config)#ip default-gateway 192.168.1.1
    S2(config)#int range f0/1-4,f0/6-17,f0/19-24,g0/1-2
    S2(config-if-range)#shutdown


#### Шаг 8.	Назначьте сети VLAN соответствующим интерфейсам коммутатора:

  1.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа:

  S1:
  
    S1(config)#int f0/6
    S1(config-if)#switchport mode access
    S1(config-if)#switchport access vlan 100

  S2:
    
    S2(config)#int f0/18
    S2(config-if)#switchport mode access
    S2(config-if)#switchport access vlan 1

  2.	Убедитесь, что VLAN назначены на правильные интерфейсы: **sh vlan**
     
##### Вопрос: Почему интерфейс F0/5 указан в VLAN 1?

    Ответ: vlan 1 является стандартным и назначается по умолчанию для портов, которые не  назначены на  другие vlan.


#### Шаг 9.	Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q:

    S1(config)#int f0/5
    S1(config-if)#switchport mode trunk
    S1(config-if)#switchport trunk native vlan 1000
    S1(config-if)#switchport trunk allowed vlan 100,200,1000
    S1(config-if)#do wr


### Часть 2.	Настройка и проверка двух серверов DHCPv4 на R1.

#### Шаг 1.	Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A:

1.	Исключите первые пять используемых адресов из каждого пула адресов
Откройте окно конфигурации
2.	Создайте пул DHCP (используйте уникальное имя для каждого пула).
3.	Укажите сеть, поддерживающую этот DHCP-сервер.
4.	В качестве имени домена укажите CCNA-lab.com.
5.	Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.
6.	Настройте время аренды на 2 дня 12 часов и 30 минут.
7.	Затем настройте второй пул DHCPv4, используя имя пула R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.

###### Подсеть A:

    R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5
    R1(config)#ip dhcp pool Subnet-A
    R1(dhcp-config)#network 192.168.1.0 255.255.255.192
    R1(dhcp-config)#domain-name CCNA-lab.com
    R1(dhcp-config)#default-router 192.168.1.1
    R1(dhcp-config)#lease 2 12 30 -  Pocket Tracer не воспринимает данную команду :(

###### Подсеть C:

    R1(config)#ip dhcp excluded-address 192.168.1.65 192.168.1.70
    R1(config)#ip dhcp pool Subnet-C
    R1(dhcp-config)#network 192.168.1.64 255.255.255.224
    R1(dhcp-config)#domain-name CCNA-lab.com
    R1(dhcp-config)#default-router 192.168.1.65
    R1(dhcp-config)#lease 2 12 30 -  Pocket Tracer не воспринимает данную команду :(

###### Пул R2_Client_LAN 

    R1(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.102
    R1(config)#ip dhcp pool R2_Client_LAN
    R1(dhcp-config)#network 192.168.1.96 255.255.255.240
    R1(dhcp-config)#domain-name CCNA-lab.com
    R1(dhcp-config)#default-router 192.168.1.97
    R1(dhcp-config)#lease 2 12 30


#### Шаг 2.	Сохраняем конфигурацию.

#### Шаг 3.	Проверка конфигурации сервера DHCPv4:

  1.	Чтобы просмотреть сведения о пуле, выполните команду **show ip dhcp pool**:

![R1_dhcp_pool](https://github.com/EfremovaOD/Otus_Homeworks/blob/309ebb8d79c0f895ca21e401de403ab883a94876/photo/Homework8/DHCPv4/R1_dhcp_pool.PNG)


  2. Выполните команду **show ip dhcp bindings** для проверки установленных назначений адресов DHCP:

![R1_bindings](https://github.com/EfremovaOD/Otus_Homeworks/blob/309ebb8d79c0f895ca21e401de403ab883a94876/photo/Homework8/DHCPv4/R1_bindings.PNG)

  3.	Выполните команду **show ip dhcp server statistics** для проверки сообщений DHCP -  Pocket Tracer не воспринимает данную команду :(

#### Шаг 4.	Попытка получить IP-адрес от DHCP на PC-A:

  1.	Из командной строки компьютера PC-A выполните команду **ipconfig /all**:

![PC-A_ipcon](https://github.com/EfremovaOD/Otus_Homeworks/blob/309ebb8d79c0f895ca21e401de403ab883a94876/photo/Homework8/DHCPv4/PC-A_ipcon.PNG)

  2.	После завершения процесса обновления выполните команду **ipconfig** для просмотра новой информации об IP-адресе:

![PC-A_ipcon_v1](https://github.com/EfremovaOD/Otus_Homeworks/blob/309ebb8d79c0f895ca21e401de403ab883a94876/photo/Homework8/DHCPv4/PC-A_ipcon_v1.PNG)

  3.	Проверьте подключение с помощью **ping** IP-адреса интерфейса R1 G0/0/1:

![PC-A_ping_R1](https://github.com/EfremovaOD/Otus_Homeworks/blob/309ebb8d79c0f895ca21e401de403ab883a94876/photo/Homework8/DHCPv4/PC-A_ping_R1.PNG)


### Часть 3.	Настройка и проверка DHCP-ретрансляции на R2.

#### Шаг 1.	Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1:

  1.	Настройте команду **ip helper-address** на G0/0/1, указав IP-адрес G0/0/0 R1:

    R2(config)#int g0/0/1
    R2(config-if)#ip helper-address 10.0.0.1

  2.	Сохраняем конфигурацию.

#### Шаг 2.	Попытка получить IP-адрес от DHCP на PC-B:

  1.	Из командной строки компьютера PC-B выполните команду **ipconfig /all**:

![PC-B_ipcon](https://github.com/EfremovaOD/Otus_Homeworks/blob/daeef80ff3ad08f15484b763591cd339ef0e6658/photo/Homework8/DHCPv4/PC-B_ipconf.PNG)

  2.	После завершения процесса обновления выполните команду **ipconfig** для просмотра новой информации об IP-адресе:

![PC-B_ipcon_v1](https://github.com/EfremovaOD/Otus_Homeworks/blob/daeef80ff3ad08f15484b763591cd339ef0e6658/photo/Homework8/DHCPv4/PC-B_ipconf_v1.PNG)

  3.	Проверьте подключение с помощью **ping** IP-адреса интерфейса R1 G0/0/1:

![PC-B_ping_R1](https://github.com/EfremovaOD/Otus_Homeworks/blob/daeef80ff3ad08f15484b763591cd339ef0e6658/photo/Homework8/DHCPv4/PC-B_ping_R1.PNG)

  4.	Выполните **show ip dhcp binding** для R1 для проверки назначений адресов в DHCP:

![R1_bind](https://github.com/EfremovaOD/Otus_Homeworks/blob/daeef80ff3ad08f15484b763591cd339ef0e6658/photo/Homework8/DHCPv4/R1_bindi.PNG)

  5.	Выполните команду **show ip dhcp server statistics** для проверки сообщений DHCP:

    К сожалению в Pocket Tracer данная команда не реализована.






















