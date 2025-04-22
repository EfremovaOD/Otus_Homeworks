# Лабораторная работа. Настройка протокола OSPFv2 для одной области
## Топология

![topology]()

## Таблица адресации 

| Устройство  | Интерфейс  | IPv6-адрес | Маска подсети |
| :------ |:------------:| --------:| ---------------:|
| R1      | G0/0/1       | 10.53.0.1    | 255.255.255.0 |
| R1      | Loopback 1   | 172.16.1.1   | 255.255.255.0 |
| R2      | G0/0/1       | 10.53.0.2    | 255.255.255.0 |
| R2      | Loopback 1   | 192.168.1.1  | 255.255.255.0 |


## Задачи:

### Часть 1. Создание сети и настройка основных параметров устройства.

### Часть 2. Настройка и проверка базовой работы протокола  OSPFv2 для одной области.

### Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области.

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


### Часть 2. Настройка и проверка базовой работы протокола  OSPFv2 для одной области.

##### Шаг 1. Настройте адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе:

1.	Настройте адреса интерфейсов на каждом маршрутизаторе, как показано в таблице адресации выше.
Откройте окно конфигурации
2.	Перейдите в режим конфигурации маршрутизатора OSPF, используя идентификатор процесса 56.
3.	Настройте статический идентификатор маршрутизатора для каждого маршрутизатора (1.1.1.1 для R1, 2.2.2.2 для R2).
4.	Настройте инструкцию сети для сети между R1 и R2, поместив ее в область 0.
5.	Только на R2 добавьте конфигурацию, необходимую для объявления сети Loopback 1 в область OSPF 0.

###### R1:

    R1#conf t
    R1(config)#int g0/0/1
    R1(config-if)#ip address 10.53.0.1 255.255.255.0
    R1(config-if)#no shutdown
    R1(config-if)#int loopback1
    R1(config-if)#ip addr 172.16.1.1 255.255.255.0
    R1(config-if)#no shutdown
    R1(config-if)#router ospf 56
    R1(config-router)#router-id 1.1.1.1
    R1(config-router)#network 10.53.0.0 0.0.0.255 area 0


###### R2:

    R2#conf t
    R2(config)#int g0/0/1
    R2(config-if)#ip address 10.53.0.2 255.255.255.0
    R2(config-if)#no shutdown
    R2(config-if)#int loopback1
    R2(config-if)#ip addr 192.168.1.1 255.255.255.0
    R2(config-if)#no shutdown
    R2(config-if)#router ospf 56
    R2(config-router)#router-id 2.2.2.2
    R2(config-router)#network 10.53.0.0 0.0.0.255 area 0
    R2(config-router)#network 192.168.1.0 0.0.0.255 area 0

6.	Убедитесь, что OSPFv2 работает между маршрутизаторами. Выполните команду, чтобы убедиться, что R1 и R2 сформировали смежность:

  ![R1_sh_ip_route]() 

  ![R2_sh_ip_route]() 

7.	На R1 выполните команду show ip route ospf, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание, что поведение OSPF по умолчанию заключается в объявлении интерфейса обратной связи в качестве маршрута узла с использованием 32-битной маски.

  ![R1_sh_ip_route_ospf]() 
  
9.	Запустите Ping до  адреса интерфейса R2 Loopback 1 из R1. Выполнение команды ping должно быть успешным.
 
  ![R1_ping]()

 ### Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области.

#### Шаг 1. Реализация различных оптимизаций на каждом маршрутизаторе:

1.	На R1 настройте приоритет OSPF интерфейса G0/0/1 на 50, чтобы убедиться, что R1 является назначенным маршрутизатором.
2.	Настройте таймеры OSPF на G0/0/1 каждого маршрутизатора для таймера приветствия, составляющего 30 секунд.
3.	На R1 настройте статический маршрут по умолчанию, который использует интерфейс Loopback 1 в качестве интерфейса выхода. Затем распространите маршрут по умолчанию в OSPF. Обратите внимание на сообщение консоли после установки маршрута по умолчанию.
4.	добавьте конфигурацию, необходимую для OSPF для обработки R2 Loopback 1 как сети точка-точка. Это приводит к тому, что OSPF объявляет Loopback 1 использует маску подсети интерфейса.
5.	Только на R2 добавьте конфигурацию, необходимую для предотвращения отправки объявлений OSPF в сеть Loopback 1.
6.	Измените базовую пропускную способность для маршрутизаторов. После этой настройки перезапустите OSPF с помощью команды clear ip ospf process . Обратите внимание на сообщение консоли после установки новой опорной полосы пропускания.

###### R1:

R1(config)#int g0/0/1
R1(config-if)#ip ospf priority 50
R1(config-if)#ip ospf hello-interval 30
R1(config-if)#ip ospf dead-interval 120
R1(config-if)#exit
R1(config)#ip route 0.0.0.0 0.0.0.0 loopback 1
R1(config)#router ospf 56
R1(config-router)#default-information originate
R1(config)#exit
R1(config)#router ospf 56
R1(config-router)#auto-cost reference-bandwidth 1000
R1(config-router)#end
R1#clear ip ospf process
R1#Reset ALL OSPF processes? [no]: y

 ###### R2:

R2(config)#int g0/0/1
R2(config-if)#ip ospf hello-interval 30
R2(config-if)#ip ospf dead-interval 120
R2(config-if)#int loop 1
R2(config-if)#ip ospf network point-to-point
R2(config-if)#exit
R2(config)#router ospf 56
R2(config-router)#passive-interface loop 1
R2(config-router)#auto-cost reference-bandwidth 1000
R2(config-router)#end
R2#clear ip ospf process
R2#Reset ALL OSPF processes? [no]: y

#### Шаг 2. Убедитесь, что оптимизация OSPFv2 реализовалась.

  1.	Выполните команду **show ip ospf interface g0/0/1** на R1 и убедитесь, что приоритет интерфейса установлен равным 50, а временные интервалы — Hello 30, Dead 120, а тип сети по умолчанию — Broadcast:

![R1_sh_ip_ospf_int_g0]()


  2.	На R1 выполните команду **show ip route ospf**, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание на разницу в метрике между этим выходным и предыдущим выходным. Также обратите внимание, что маска теперь составляет 24 бита, в отличие от 32 битов, ранее объявленных:

![R1_sh_ip_route_ospf_]()

  3.	Введите команду **show ip route ospf** на маршрутизаторе R2. Единственная информация о маршруте OSPF должна быть распространяемый по умолчанию маршрут R1:

![R2_sh_ip_route_ospf]()

  4.	Запустите **Ping** до адреса интерфейса R1 Loopback 1 из R2. Выполнение команды **ping** должно быть успешным:

![R2_ping]()
    
