# Лабраторная работа - Настройка DHCPv6
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/2dee51141b1a4cf28bdd41d1f5469785b38b93aa/photo/Homework7/Topology.PNG)

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
    S1(config)#service pass
    S1(config)#banner motd #*************************ATTENTION********************************#
    S1(config-if)#int range f0/1-24, g0/2
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









    
