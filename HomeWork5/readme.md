# Лабораторная работа. Доступ к сетевым устройствам по протоколу SSH
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/c625aadd110538986bfd1673cc7b7894517ac375/photo/Homework5/Topology.png)



## Таблица адресации 

| Устройство  | Интерфейс  | IP-адрес | Маска подсети | Шлюз по умолчанию |
| :------------ |:---------------:| -----:| -----:| -----:|
| R1      | G0/0/1 | 192.168.1.1  | 255.255.255.0 | - |
| S1      | VLAN 1 | 192.168.1.11 | 255.255.255.0 | 192.168.1.1 |
| PC-A    | NIC    | 192.168.1.3  | 255.255.255.0 | 192.168.1.1  |


## Задачи:

### Часть 1. Настройка основных параметров устройства.

### Часть 2. Настройка маршрутизатора для доступа по протоколу SSH.

### Часть 3. Настройка коммутатора для доступа по протоколу SSH.

### Часть 4. SSH через интерфейс командной строки (CLI) коммутатора.

## Решение:

### Часть 1. Настройка основных параметров устройств.

#### Шаг 1. Создаем сеть согласно топологии.

#### Шаг 2. Выполняем  инициализацию и перезагрузку маршрутизатора и коммутатора.

#### Шаг 3. Настройте маршрутизатор:

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
        *************************************************************************************ATTENTION*******************************************************************************    *************#
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
    R1(config-if)#ip address 192.168.1.1 255.255.255.0
    R1(config-if)#end
    R1#copy running-config startup-config

#### Шаг 4. Настройте компьютер PC-A:

![PC-A_IP](https://github.com/EfremovaOD/Otus_Homeworks/blob/c625aadd110538986bfd1673cc7b7894517ac375/photo/Homework5/PC-A_IP.png)

#### Шаг 5. Проверьте подключение к сети c PC-A до R1:

![PC-A_Ping](https://github.com/EfremovaOD/Otus_Homeworks/blob/c625aadd110538986bfd1673cc7b7894517ac375/photo/Homework5/PC-A_Ping_R1.png)

### Часть 2. Настройка маршрутизатора для доступа по протоколу SSH.

#### Шаг 1. Настройте аутентификацию устройств:

  1. Зададим доменное имя устройства:

    R1(config)#
    R1(config)#ip domain-name kapibara.ru

#### Шаг 2. Создайте ключ шифрования с указанием его длины:

    R1(config)#crypto key generate rsa
    The name for the keys will be: R1.kapibara.ru
    Choose the size of the key modulus in the range of 360 to 2048 for your
      General Purpose Keys. Choosing a key modulus greater than 512 may take
      a few minutes.
    
    How many bits in the modulus [512]: 2048
    % Generating 2048 bit RSA keys, keys will be non-exportable...[OK]

#### Шаг 3. Создайте имя пользователя в локальной базе учетных записей:

    R1(config)#username admin privilege 15 secret Adm1nP@55

#### Шаг 4. Активируйте протокол SSH на линиях VTY:

    R1(config)#line vty 0 4
    R1(config-line)#
    R1(config-line)#login local
    R1(config-line)#
    R1(config-line)#transport input ssh
    R1(config-line)#

#### Шаг 5. Сохраните текущую конфигурацию в файл загрузочной конфигурации:

    R1#copy running-config startup-config

#### Шаг 6. Установите соединение с маршрутизатором по протоколу SSH:

![PC-A_SSH](https://github.com/EfremovaOD/Otus_Homeworks/blob/c625aadd110538986bfd1673cc7b7894517ac375/photo/Homework5/PC-A_SSH.png)

### Часть 3. Настройка коммутатора для доступа по протоколу SSH.

#### Шаг 1. Настройте основные параметры коммутатора:

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
    *******************ATTENTION*:3****************************#
    
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
    
    S1(config-if)#ip add
    S1(config-if)#ip address 192.168.1.11  255.255.255.0
    S1(config-if)#end
    S1#copy running-config startup-config 

#### Шаг 2. Настройте коммутатор для соединения по протоколу SSH:

    S1#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    S1(config)#
    S1(config)#ip domain-name kapibara_s1.ru
    S1(config)#
    S1(config)#crypto key generate rsa
    The name for the keys will be: S1.kapibara_s1.ru
    Choose the size of the key modulus in the range of 360 to 2048 for your
      General Purpose Keys. Choosing a key modulus greater than 512 may take
      a few minutes.
    
    How many bits in the modulus [512]: 2048
    % Generating 2048 bit RSA keys, keys will be non-exportable...[OK]
    
    S1(config)#
    *Mar 1 1:25:27.574: %SSH-5-ENABLED: SSH 1.99 has been enabled
    S1(config)#username admin privi
    S1(config)#username admin privilege 15 sec
    S1(config)#username admin privilege 15 secret Adm1nP@55
    S1(config)#
    S1(config)#line vty 0 4
    S1(config-line)#login local
    S1(config-line)#
    S1(config-line)#trans
    S1(config-line)#transport input ssh
    S1(config-line)#

#### Шаг 3. Установите соединение с коммутатором по протоколу SSH:

![PC-A_SSH_S1](https://github.com/EfremovaOD/Otus_Homeworks/blob/c625aadd110538986bfd1673cc7b7894517ac375/photo/Homework5/PC-A_SSH_S1.png)

### Часть 4. Настройка протокола SSH с использованием интерфейса командной строки (CLI) коммутатора.

#### Шаг 1. Посмотрите доступные параметры для клиента SSH в Cisco IOS:

![SSH_S1](https://github.com/EfremovaOD/Otus_Homeworks/blob/c625aadd110538986bfd1673cc7b7894517ac375/photo/Homework5/SSH.png)

#### Шаг 2. Установите с коммутатора S1 соединение с маршрутизатором R1 по протоколу SSH.

![SSH_R1](https://github.com/EfremovaOD/Otus_Homeworks/blob/c625aadd110538986bfd1673cc7b7894517ac375/photo/Homework5/SSH_R1.png)






