# Лабораторная работа. Настройка и проверка расширенных списков контроля доступа.
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/Topology.PNG)

## Таблица адресации 

| Устройство  | Интерфейс  | IPv6-адрес | Маска подсети | Шлюз по умолчанию |
| :------ |:------------:| --------:| ---------------:| ---------------:|
| R1      | G0/0/1       | -    | - | - |
| R1      | G0/0/1.20      | 10.20.0.1   | 255.255.255.0 | - |
| R1      | G0/0/1.30      | 10.30.0.1   | 255.255.255.0 | - |
| R1      | G0/0/1.40      | 10.40.0.1   | 255.255.255.0 | - |
| R1      | G0/0/1.1000    | -    | - | - |
| R1      | Loopback 1   | 172.16.1.1   | 255.255.255.0 | - |
| R2      | G0/0/1       | 10.20.0.4    | 255.255.255.0 | - |
| S1      | VLAN 20 | 10.20.0.2  | 255.255.255.0 | 10.20.0.1 |
| S2      | VLAN 20 | 10.20.0.3  | 255.255.255.0 | 10.20.0.1 |
| PC-A    | NIC     | 10.30.0.2  | 255.255.255.0 | 10.30.0.1 |
| PC-B    | NIC     | 10.40.0.2  | 255.255.255.0 | 10.40.0.1 |

## Таблица VLAN 

| VLAN  | Имя  | Назначенный интерфейс |
| :------ |:------------:| --------:|
| 20       | Management   | S2: f0/5 |
| 30       | Operations   | S2: f0/16 |
| 40       | Sales        | S2: f0/18 |
| 999      | ParkingLot   | S1: F0/2-4, F0/7-24, G0/1-2 S2: F0/2-4, F0/6-17, F0/19-24, G0/1-2|
| 1000     | NATIVE       | - |



## Задачи:

### Часть 1. Создание сети и настройка основных параметров устройства.

### Часть 2. Настройка и проверка списков расширенного контроля доступа.

### Часть 3. ·Настройте транки (магистральные каналы).

### Часть 4. Настройте маршрутизацию.

### Часть 5. Настройте удаленный доступ.

### Часть 6. Проверка подключения.

### Часть 7. Настройка и проверка списков контроля доступа (ACL).


## Решение:

### Часть 1. Создание сети и настройка основных параметров устройства.

#### Шаг 1.	Создаем сеть согласно топологии (см.пункт "Топология") и инициализация устройств.

#### Шаг 2.	Произведите базовую настройку маршрутизаторов:

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
    R1(config)#line console 0
    R1(config-line)#password cisco
    R1(config-line)#login
    R1(config-line)#line vty 0 4
    R1(config-line)#password cisco
    R1(config-line)#login
    R1(config-line)#end
    R1#copy running-config startup-config

#### Шаг 3. Настройте базовые параметры каждого коммутатора.

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
    S1(config)#line console 0
    S1(config-line)#
    S1(config-line)#pass cisco
    S1(config-line)#login
    S1(config-line)#line vty 0 4
    S1(config-line)#pass cisco
    S1(config-line)#login
    S1(config-line)#
    S1(config-line)#end
    S1#copy running-config startup-config 

### Часть 2. Настройка сетей VLAN на коммутаторах.

#### Шаг 1. Создайте сети VLAN на коммутаторах.

1. Создайте необходимые VLAN и назовите их на каждом коммутаторе из приведенной выше таблицы.
2.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации. 
3.	Назначьте все неиспользуемые порты коммутатора VLAN Parking Lot, настройте их для статического режима доступа и административно деактивируйте их.
Примечание. Команда interface range полезна для выполнения этой задачи с помощью необходимого количества команд. 


##### S1

    S1(config)#vlan 20
    S1(config-vlan)#name Management    
    S1(config-vlan)#vlan 30
    S1(config-vlan)#name Operations
    S1(config-vlan)#vlan 40
    S1(config-vlan)#name Sales
    S1(config-vlan)#vlan 999
    S1(config-vlan)#name ParkingLot
    S1(config-vlan)#int vlan 20
    S1(config-if)#ip addr 10.20.0.2 255.255.255.0
    S1(config-if)#no sh
    S1(config-if)#exit
    S1(config)#ip default-gateway 10.20.0.1
    S1(config)#int range f0/2-4, f0/7-24, g0/1-2
    S1(config-if-range)#switchport mode access
    S1(config-if-range)#switchport access vl 999
    S1(config-if-range)#sh
    S1(config-if-range)#end

##### S2 

    S2(config)#vlan 20
    S2(config-vlan)#name Management
    S2(config-vlan)#vlan 30
    S2(config-vlan)#name Operations
    S2(config-vlan)#vlan 40
    S2(config-vlan)#name Sales
    S2(config-vlan)#vlan 999
    S2(config-vlan)#name ParkingLot
    S2(config-vlan)#int vlan 20
    S2(config-if)#ip addr 10.20.0.3 255.255.255.0
    S2(config-if)#no sh
    S2(config-if)#ex
    S2(config)#ip default-gateway 10.20.0.1
    S2(config)#int range f0/2-4, f0/6-17, f0/19-24, g0/1-2
    S2(config-if-range)#switchport mode access
    S2(config-if-range)#switchport access vl 999
    S2(config-if-range)#sh
    S2(config-if-range)#end

#### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

1.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
2.	Выполните команду **show vlan brief**, чтобы убедиться, что сети VLAN назначены правильным интерфейсам.

##### S1:

    S1(config)#int f0/6
    S1(config-if)#switchport mode access
    S1(config-if)#switchport access vlan 30

![S1_sh_vlan_brief](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/S1_sh_vlan_brief.PNG)

##### S2:

    S2(config)#int f0/5
    S2(config-if)#switchport mode access
    S2(config-if)#switchport access vlan 20
    S2(config-if)#int f0/18
    S2(config-if)#switchport mode access
    S2(config-if)#switchport access vlan 40

![S2_sh_vlan_brief](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/S2_sh_vlan_brief.PNG)

### Часть 3. ·Настройте транки (магистральные каналы).

#### Шаг 1. Вручную настройте магистральный интерфейс F0/1.

1.	Измените режим порта коммутатора на интерфейсе F0/1, чтобы принудительно создать магистральную связь. Не забудьте сделать это на обоих коммутаторах.
2.	В рамках конфигурации транка установите для native vlan значение 1000 на обоих коммутаторах. При настройке двух интерфейсов для разных собственных VLAN сообщения об ошибках могут отображаться временно.
3.	В качестве другой части конфигурации транка укажите, что VLAN 10, 20, 30 и 1000 разрешены в транке.
4.	Выполните команду **show interfaces trunk** для проверки портов магистрали, собственной VLAN и разрешенных VLAN через магистраль.

        S2(config)#int f0/1
        S2(config-if)#switchport mode trunk
        S2(config-if)#switchport trunk native vlan 1000
        S2(config-if)#switchport trunk allowed vl 10,20,30,1000

![S2_sh_int_tr](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/S2_sh_int_tr.PNG)

#### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.

1.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.
2.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
3.	Используйте команду **show interfaces trunk** для проверки настроек транка.

        S1(config)#int f0/5
        S1(config-if)#switchport mode trunk
        S1(config-if)#switchport trunk native vlan 1000
        S1(config-if)#sw tr allowed vl 10,20,30,1000
        S1(config-if)#end
        S1#copy running-config startup-config 

![S1_sh_int_tr](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/S1_sh_int_tr.PNG)

### Часть 4. Настройте маршрутизацию.

#### Шаг 1. Настройка маршрутизации между сетями VLAN на R1.

1.	Активируйте интерфейс G0/0/1 на маршрутизаторе.
2.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейс для собственной VLAN не имеет назначенного IP-адреса. Включите описание для каждого подинтерфейса.
3.	Настройте интерфейс Loopback 1 на R1 с адресацией из приведенной выше таблицы.
4.	С помощью команды **show ip interface brief** проверьте конфигурацию подынтерфейса.

         R1(config)#int g0/0/1
         R1(config-if)#no sh
         R1(config-if)#int g0/0/1.20
         R1(config-subif)#encapsulation dot1Q 20
         R1(config-subif)#ip addr 10.20.0.1 255.255.255.0
         R1(config-subif)#description Management
         R1(config-subif)#no sh
         R1(config-subif)#int g0/0/1.30
         R1(config-subif)#encapsulation dot1Q 30
         R1(config-subif)#ip addr 10.30.0.1 255.255.255.0
         R1(config-subif)#description Operations
         R1(config-subif)#no sh
         R1(config-subif)#int g0/0/1.40
         R1(config-subif)#encapsulation dot1Q 40
         R1(config-subif)#ip addr 10.40.0.1 255.255.255.0
         R1(config-subif)#description Sales
         R1(config-subif)#no sh
         R1(config-subif)#int g0/0/1.1000
         R1(config-subif)#description NATIVE
         R1(config-subif)#no sh
         R1(config-subif)#int loop 1
         R1(config-if)#ip addr 172.16.1.1 255.255.255.0
         R1(config-if)#no sh


![R1_sh_ip_int_br](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/R1_sh_ip_int_br.PNG)

#### Шаг 2. Настройка интерфейса R2 g0/0/1 с использованием адреса из таблицы и маршрута по умолчанию с адресом следующего перехода 10.20.0.1

    R2(config)#int g0/0/1
    R2(config-if)#ip addr 10.20.0.4 255.255.255.0
    R2(config-if)#no sh
    R2(config-if)#ex
    R2(config)#ip route 0.0.0.0 0.0.0.0 10.20.0.1

### Часть 5. Настройте удаленный доступ.

#### Шаг 1. Настройте все сетевые устройства для базовой поддержки SSH.

1.	Создайте локального пользователя с именем пользователя **SSHadmin** и зашифрованным паролем **$cisco123!**
2.	Используйте ccna-lab.com в качестве доменного имени.
3.	Генерируйте криптоключи с помощью 1024 битного модуля.
4.	Настройте первые пять линий VTY на каждом устройстве, чтобы поддерживать только SSH-соединения и с локальной аутентификацией.

         S1(config)#username SSHadmin privilege 15 secret $cisco123!
         S1(config)#ip domain-name ccna-lab.com
         S1(config)#crypto key generate rsa
         S1(config)#ip ssh ver 2
         S1(config)#line vty 0 4
         S1(config-line)#login local
         S1(config-line)#transport input ssh

#### Шаг 2. Включите защищенные веб-службы с проверкой подлинности на R1.

1.	Включите сервер HTTPS на R1.
**R1(config)# ip http secure-server**
2.	Настройте R1 для проверки подлинности пользователей, пытающихся подключиться к веб-серверу.
**R1(config)# ip http authentication local**

В CPT нет возможности настройки http сервера на cisco. Вместо будет https сервер, адреса: **10.20.0.20** и **172.16.2.20**, подключение осуществлено через S1: f0/7:

    S1(config)#intint f0/7
    S1(config-if)#switchport mode access
    S1(config-if)#switchport access vl 20
    S1(config-if)#description SERV
    S1(config-if)#no sh

### Часть 6. Проверка подключения.

#### Шаг 1. Настройте узлы ПК.

Назначаем адреса в соответствии с таблицей адресации.

#### Шаг 2. Выполните следующие тесты. Эхозапрос должен пройти успешно.

*Опираясь на предыдущее задание только VLAN 10, 20, 30 и 1000 были разрешены магистральному каналу, поэтому ping на 10.40.0.10 не успешный. Если добить vlan 40 в магистральный канал, то все работает.*


| От  | Протокол  | Назначение |
| :------ |:------------:| --------:|
| PC-A       | Ping   | 10.40.0.10 |
| PC-A       | Ping   | 10.20.0.1|
| PC-B       | Ping        | 10.30.0.10 |
| PC-B       | Ping        | 10.20.0.1 |
| PC-B       | Ping        | 172.16.1.1 |
| PC-B       | HTTPS        | 10.20.0.1 |
| PC-B       | HTTPS        | 172.16.1.1 |
| PC-B       | SSH        | 10.20.0.1 |
| PC-B       | SSH        | 172.16.1.1 |

![PC-A_ping_1](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-A_ping_1.PNG)
![PC-A_ping_2](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-A_ping_2.PNG)
![PC-B_ping_1](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_ping_1.PNG)
![PC-B_ping_2](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_ping_2.PNG)
![PC-B_ping_3](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_ping_3.PNG)
![PC-B_https_1](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_https_1.PNG)
![PC-B_https_2](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_https_2.PNG)

*В связи с доп. поставленным сервером имитируем loopback, внесенные изменения:*

    R1(config)#int g0/0/1.172
    R1(config-subif)#ip addr 172.16.2.1 255.255.255.0
    R1(config-subif)#no sh

    S1(config)#vlan 172
    S1(config-vlan)#name loop_web
    S1(config-vlan)#int f0/5
    S1(config-if)#switchport trunk all vlan add 172
    S1(config-if)#int f0/21
    S1(config-if)#switchport mode access
    S1(config-if)#switchport access vlan 172
    S1(config-if)#no sh

![PC-B_ssh_1](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_ssh_1.PNG)
![PC-B_ssh_2](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_ssh_2.PNG)

### Часть 7. Настройка и проверка списков контроля доступа (ACL).

*При проверке базового подключения компания требует реализации следующих политик безопасности:
Политика1. Сеть Sales не может использовать SSH в сети Management (но в  другие сети SSH разрешен). 
Политика 2. Сеть Sales не имеет доступа к IP-адресам в сети Management с помощью любого веб-протокола (HTTP/HTTPS). Сеть Sales также не имеет доступа к интерфейсам R1 с помощью любого веб-протокола. Разрешён весь другой веб-трафик (обратите внимание — Сеть Sales  может получить доступ к интерфейсу Loopback 1 на R1).
Политика3. Сеть Sales не может отправлять эхо-запросы ICMP в сети Operations или Management. Разрешены эхо-запросы ICMP к другим адресатам. 
Политика 4: Cеть Operations  не может отправлять ICMP эхозапросы в сеть Sales. Разрешены эхо-запросы ICMP к другим адресатам.*


#### Шаг 1. Проанализируйте требования к сети и политике безопасности для планирования реализации ACL.

#### Шаг 2. Разработка и применение расширенных списков доступа, которые будут соответствовать требованиям политики безопасности.

    R1(config)#ip access-list extended SALES
    R1(config-ext-nacl)#10 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 22
    R1(config-ext-nacl)#30 deny icmp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255
    R1(config-ext-nacl)#deny icmp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255
    R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 range www 443
    R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.10.0.1 range www 443
    R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.30.0.1 range www 443
    R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.40.0.1 range www 443
    R1(config-ext-nacl)#permit ip any any
    R1(config-ext-nacl)#int g0/0/1.40
    R1(config-subif)#ip access-group SALES in
    R1(config-subif)#exit
    R1(config)#ip access-list extended OPERATIONS
    R1(config-ext-nacl)#deny icmp 10.30.0.0 0.0.0.255 10.40.0.0 0.0.0.255
    R1(config-ext-nacl)#permit ip any any
    R1(config-ext-nacl)#int gig0/0/1.30
    R1(config-subif)#ip access-group OPERATIONS in



#### Шаг 3. Убедитесь, что политики безопасности применяются развернутыми списками доступа.

| От  | Протокол  | Назначение | Результат |
| :------ |:------------:| --------:| --------:|
| PC-A       | Ping   | 10.40.0.10 | Сбой |
| PC-A       | Ping   | 10.20.0.1| Успех |
| PC-B       | Ping        | 10.30.0.10 | Сбой |
| PC-B       | Ping        | 10.20.0.1 | Сбой |
| PC-B       | Ping        | 172.16.1.1 | Успех |
| PC-B       | HTTPS        | 10.20.0.1 | Сбой |
| PC-B       | HTTPS        | 172.16.1.1 | Успех |
| PC-B       | SSH        | 10.20.0.1 | Сбой |
| PC-B       | SSH        | 172.16.1.1 | Успех |

![PC-A_ping_all](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-A_ping_all.PNG)
![PC-B_ping_all](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_ping_all.PNG)
![PC-B_https_1_v1](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_https_1_v1.PNG)
![PC-B_https_2_v1](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_https_2_v1.PNG)
![PC-B_ssh_2_v1](https://github.com/EfremovaOD/Otus_Homeworks/blob/256320132a186c60786a764c73aca1fe0b97d586/photo/Homework11/PC-B_ssh_2_v1.PNG)







