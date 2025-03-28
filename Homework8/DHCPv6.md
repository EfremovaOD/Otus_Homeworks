# Лабраторная работа - Настройка DHCPv6
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/21ecdff600ded223610c661a44b6f8565f1d59b3/photo/Homework8/DHCPv6/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA.PNG)

## Таблица адресации 

| Устройство  | Интерфейс  | IPv6-адрес |
| :---------- |:---------------:| -----:|
| R1      | G0/0/0   | 2001:db8:acad:2::1/64 |
| R1      | G0/0/0   | fe80::1               |
| R1      | G0/0/1   | 2001:db8:acad:1::1/64 |
| R1      | G0/0/1   | fe80::1               |
| R2      | G0/0/0   | 2001:db8:acad:2::2/64 |
| R2      | G0/0/0   | fe80::2               |
| R2      | G0/0/1   | 2001:db8:acad:3::1/64 |
| R2      | G0/0/1   | fe80::1               |
| PC-A    | NIC      | DHCP                  |
| PC-B    | NIC      | DHCP                  |


## Задачи:

### Часть 1. Создание сети и настройка основных параметров устройства.

### Часть 2. Проверка назначения адреса SLAAC от R1.

### Часть 3. Настройка и проверка сервера DHCPv6 без гражданства на R1.

### Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1.

### Часть 5. Настройка и проверка DHCPv6 Relay на R2.

## Решение:

### Часть 1. Создание сети и настройка основных параметров устройства.

#### Шаг 1. Создаем сеть согласно топологии.

#### Шаг 2.	Настраеваем базовые параметры каждого коммутатора.

  1. На примере S1:

    Switch>en
    Switch#conf t
    Switch(config)#no ip domain-lookup
    Switch(config)#hostname S1
    S1(config)#enable secret class
    S1(config)#line console 0
    S1(config-line)#password cisco
    S1(config-line)#login
    S1(config-line)#line vty 0 4
    S1(config-line)#password cisco
    S1(config-line)#login
    S1(config-line)#exit
    S1(config)#service password-encryption
    S1(config)#banner motd #*************************ATTENTION********************************#
    S1(config-if)#int range f0/1-4,f0/7-24,g0/1-2
    S1(config-if-range)#shutdown
    S1(config-if-range)#end
    S1#copy running-config startup-config

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
    R1(config)#line console 0
    R1(config-line)#password cisco
    R1(config-line)#login
    R1(config-line)#exit
    R1(config)#line vty 0 4
    R1(config-line)#password cisco
    R1(config-line)#login
    R1(config-line)#exit
    R1(config)#sdm prefer dual-ipv4-and-ipv6 default
    R1(config)#end
    R1#copy running-config startup-config

#### Шаг 4. Настройка интерфейсов и маршрутизации для обоих маршрутизаторов.

  1. Настройте интерфейсы G0/0/0 и G0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше(На примере R1):

    R1(config)#int g0/0/0
    R1(config-if)#ipv6 address 2001:db8:acad:2::1/64
    R1(config-if)#ipv6 address FE80::1 link-local
    R1(config-if)#int g0/0/1
    R1(config-if)#ipv6 add 2001:db8:acad:1::1/64
    R1(config-if)#ipv6 add fe80::1 link-local
    R1(config-if)# no shutdown

  2. Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес G0/0/0 на другом маршрутизаторе:

  a. R1:
  
    R1(config)#ipv6 route ::0/0 2001:db8:acad:2::2

  b. R2:
    
    R2(config)#ipv6 route ::0/0 2001:db8:acad:2::1


  3. Убедитесь, что маршрутизация работает с помощью пинга адреса G0/0/1 R2 из R1:

![Ping_R1_R2](https://github.com/EfremovaOD/Otus_Homeworks/blob/e0cb15b2724fc9c1f33756f1bb73f9cac4d27cfc/photo/Homework8/DHCPv6/Ping_R1_to_R2.PNG)

  4. Сохраняем текущую конфигурацию.

### Часть 2. Проверка назначения адреса SLAAC от R1.

  1. Включите PC-A и убедитесь, что сетевой адаптер настроен для автоматической настройки IPv6:

![PC-A_DHCP](https://github.com/EfremovaOD/Otus_Homeworks/blob/e0cb15b2724fc9c1f33756f1bb73f9cac4d27cfc/photo/Homework8/DHCPv6/PC-A_DHCP.PNG)

  2. Через несколько минут результаты команды **ipconfig** должны показать, что PC-A присвоил себе адрес из сети 2001:db8:1::/64:

![PC-A_IPv6](https://github.com/EfremovaOD/Otus_Homeworks/blob/e0cb15b2724fc9c1f33756f1bb73f9cac4d27cfc/photo/Homework8/DHCPv6/PC-A_IPv6.PNG)


### Часть 3. Настройка и проверка сервера DHCPv6 без гражданства на R1.

####  Шаг 1. Более подробно изучите конфигурацию PC-A:

![PC-A_ipconfig](https://github.com/EfremovaOD/Otus_Homeworks/blob/e0cb15b2724fc9c1f33756f1bb73f9cac4d27cfc/photo/Homework8/DHCPv6/PC-A_ipconfig.PNG)

#### Шаг 2. Настройте R1 для предоставления DHCPv6 без состояния для PC-A:

  1.  Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS. В составе этого пула назначьте адрес DNS-сервера как 2001:db8:acad::1, а имя домена — как stateless.com:

    R1(config)# ipv6 dhcp pool R1-STATELESS
    R1(config-dhcp)# dns-server 2001:db8:acad::254
    R1(config-dhcp)# domain-name STATELESS.com

  2. Настройте интерфейс G0/0/1 на R1, чтобы предоставить флаг конфигурации OTHER для локальной сети R1 и укажите только что созданный пул DHCP в качестве ресурса DHCP для этого интерфейса:

    R1(config)# int g0/0/1
    R1(config-if)# ipv6 nd other-config-flag 
    R1(config-if)# ipv6 dhcp server R1-STATELESS

  3. Сохраняем конфигурацию R1 и перезагружаем PC-A.
  4.	Проверьте вывод **ipconfig /all** и обратите внимание на изменения:

![PC-A_ipconfig_v1](https://github.com/EfremovaOD/Otus_Homeworks/blob/e0cb15b2724fc9c1f33756f1bb73f9cac4d27cfc/photo/Homework8/DHCPv6/PC-A_ipconfig_v1.PNG)

  5. Тестирование подключения с помощью **ping** IP-адреса интерфейса G0/1 R2:

![PC-A_ping_R2](https://github.com/EfremovaOD/Otus_Homeworks/blob/e0cb15b2724fc9c1f33756f1bb73f9cac4d27cfc/photo/Homework8/DHCPv6/PC-A_Ping_R2.PNG)


### Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1.

  1. Создайте пул DHCPv6 на R1 для сети 2001:db8:acad:3:aaa::/80. Это предоставит адреса локальной сети, подключенной к интерфейсу G0/0/1 на R2. В составе пула задайте DNS-сервер 2001:db8:acad: :254 и задайте доменное имя STATEFUL.com:

    R1(config)# ipv6 dhcp pool R2-STATEFUL
    R1(config-dhcp)# address prefix 2001:db8:acad:3:aaa::/80
    R1(config-dhcp)# dns-server 2001:db8:acad::254
    R1(config-dhcp)# domain-name STATEFUL.com

  2.	Назначьте только что созданный пул DHCPv6 интерфейсу g0/0/0 на R1:

    R1(config)# interface g0/0/0
    R1(config-if)# ipv6 dhcp server R2-STATEFUL

### Часть 5. Настройка и проверка DHCPv6 Relay на R2.

#### Шаг 1. Включите PC-B и проверьте адрес SLAAC, который он генерирует:

![PC-B_ipconfig](https://github.com/EfremovaOD/Otus_Homeworks/blob/e0cb15b2724fc9c1f33756f1bb73f9cac4d27cfc/photo/Homework8/DHCPv6/PC-B_ipconfig.PNG)

#### Шаг 2. Настройте R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1:

  1.	Настройте команду **ipv6 dhcp relay** на интерфейсе R2 G0/0/1, указав адрес назначения интерфейса G0/0/0 на R1. Также настройте команду **managed-config-flag** и сохраняем  конфигурацию:

    R2 (конфигурация) # интерфейс g0/0/1
    R2(config-if)# ipv6 nd managed-config-flag
    R2(config-if)# ipv6 dhcp relay destination 2001:db8:acad:2::1 g0/0/0
    R2(config-if)# do wr

  2. К сожалению  Pocket tracer не поддерживает **ipv6 dhcp relay**,  так что настроила DHCP IPv6 на R2 вручную:

    R2(config)#ipv6 dhcp pool R2-STATEFUL
    R2(config-dhcpv6)#add prefix 2001:db8:acad:3:aaa::/80 lifetime 172800 86400
    R2(config-dhcpv6)#dns-server 2001:DB8:ACAD::254
    R2(config-dhcpv6)#domain-name STATEFUL.com
    R2(config-dhcpv6)#int g0/0/1
    R2(config-if)#ipv6 dhcp server R2-STATEFUL
    R2(config-if)#ipv6 nd managed-config-flag
    
#### Шаг 3. Попытка получить адрес IPv6 из DHCPv6 на PC-B:

  1. Перезагружаем PC-B.
  2. Откройте командную строку на PC-B и выполните команду **ipconfig /all** и проверьте выходные данные, чтобы увидеть результаты операции ретрансляции DHCPv6:

![PC-B_ipconfig_v1](https://github.com/EfremovaOD/Otus_Homeworks/blob/e0cb15b2724fc9c1f33756f1bb73f9cac4d27cfc/photo/Homework8/DHCPv6/PC-B_ipconf_v1.PNG)

  3. Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1:

![PC-B_ping_R1](https://github.com/EfremovaOD/Otus_Homeworks/blob/e0cb15b2724fc9c1f33756f1bb73f9cac4d27cfc/photo/Homework8/DHCPv6/PC-B_ping_R1.PNG)


















