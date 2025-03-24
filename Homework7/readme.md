# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/2dee51141b1a4cf28bdd41d1f5469785b38b93aa/photo/Homework7/Topology.PNG)

## Таблица адресации 

| Устройство  | Интерфейс  | IP-адрес | Маска подсети |
| :------------ |:---------------:| -----:| -----:|
| S1      | VLAN 1   | 192.168.1.1  | 255.255.255.0 |
| S2      | VLAN 1   | 192.168.1.2  | 255.255.255.0 |
| S3      | VLAN 1   | 192.168.1.3  | 255.255.255.0 |


## Задачи:

### Часть 1. Создание сети и настройка основных параметров устройства.

### Часть 2. Выбор корневого моста.

### Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов.

### Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов.

## Решение:

### Часть 1. Создание сети и настройка основных параметров устройства.

#### Шаг 1. Создаем сеть согласно топологии.

#### Шаг 2.	Выполняем инициализацию и перезагрузку коммутаторов.

#### Шаг 3.	Настройте базовые параметры каждого коммутатора:

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
    S1(config)#int vlan 1
    S1(config-if)#ip add 192.168.1.1 255.255.255.0
    S1(config-if)#no shutdown
    S1(config-if)#end
    S1#copy running-config startup-config

#### Шаг 4.	Проверьте связь.

  1. Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?

![PinG_S1_to_S2](https://github.com/EfremovaOD/Otus_Homeworks/blob/ecabb1c2b1da0951a182e86d98d8742a05761dab/photo/Homework7/Ping_S1_S2.PNG)
     
  2. Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?

![PinG_S1_to_S3](https://github.com/EfremovaOD/Otus_Homeworks/blob/ecabb1c2b1da0951a182e86d98d8742a05761dab/photo/Homework7/Ping_S1_S3.PNG)
     
  3. Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3? -

![PinG_S2_to_S3](https://github.com/EfremovaOD/Otus_Homeworks/blob/ecabb1c2b1da0951a182e86d98d8742a05761dab/photo/Homework7/Ping_S2_S3.PNG)

### Часть 2. Определение корневого моста.

#### Шаг 1. Отключите все порты на коммутаторах:

    S1(config-if)#int ra f0/1-24, g0/1-2
    S1(config-if-range)#shutdown

#### Шаг 2.	Настройте подключенные порты в качестве транковых:

    S1(config-if-range)#int range f0/1-4
    S1(config-if-range)#switchport mode trunk

#### Шаг 3.	Включите порты F0/2 и F0/4 на всех коммутаторах:

    S1(config-if-range)#int range f0/2, f0/4
    S1(config-if-range)#no shutdown

#### Шаг 4.	Отобразите данные протокола spanning-tree:

  1. S1:

    S1#show  spanning-tree 
    VLAN0001
      Spanning tree enabled protocol ieee
      Root ID    Priority    32769
                 Address     000B.BE8A.CBC7
                 This bridge is the root
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
    
      Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                 Address     000B.BE8A.CBC7
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                 Aging Time  20
    
   Interface        Role Sts Cost      Prio.Nbr Type
   ---------------- ---- --- --------- -------- --------------------------------
   Fa0/2            Desg FWD 19        128.2    P2p
   Fa0/4            Desg FWD 19        128.4    P2p


  2. S2:

   S2#show spanning-tree 
   VLAN0001
     Spanning tree enabled protocol ieee
     Root ID    Priority    32769
                Address     000B.BE8A.CBC7
                Cost        19
                Port        2(FastEthernet0/2)
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
   
     Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                Address     0030.A3C5.8617
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                Aging Time  20
   
   Interface        Role Sts Cost      Prio.Nbr Type
   ---------------- ---- --- --------- -------- --------------------------------
   Fa0/2            Root FWD 19        128.2    P2p
   Fa0/4            Desg FWD 19        128.4    P2p

  3. S3:

   S3#show spanning-tree
   VLAN0001
     Spanning tree enabled protocol ieee
     Root ID    Priority    32769
                Address     000B.BE8A.CBC7
                Cost        19
                Port        4(FastEthernet0/4)
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
   
     Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                Address     00E0.8F5E.2769
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                Aging Time  20
   
   Interface        Role Sts Cost      Prio.Nbr Type
   ---------------- ---- --- --------- -------- --------------------------------
   Fa0/2            Altn BLK 19        128.2    P2p
   Fa0/4            Root FWD 19        128.4    P2p

##### Роль и состояние активных портов на каждом коммутаторе в топологии:

![Topology_act](https://github.com/EfremovaOD/Otus_Homeworks/blob/b241741500b8051f6f91bc5755ca78593359155d/photo/Homework7/Topology_Akt.PNG)

 ### С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы:
  
  a. Какой коммутатор является корневым мостом? -  **S1**

  b. Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста? - **S1 имеет наименьший MAC**

  c. Какие порты на коммутаторе являются корневыми портами? - **на  S2: f0/2 и S3:  f0/4, S1 - корневой и не  имеет корневых портов.**

  d. Какие порты на коммутаторе являются назначенными портами? - **S1 - корневой  и все его порты являются  назначенными, S2 имет назначенный  порт: f0/4.**

  i. Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? - **S3: f0/2**

  f. Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта? - **Маршрут считается неэффективным из-за его длинны. Будет использоваться как резервный в случае отказа корневого порта.**


### Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов.

#### Шаг 1. Определите коммутатор с заблокированным портом:

   S3#show spanning-tree 
   VLAN0001
     Spanning tree enabled protocol ieee
     Root ID    Priority    32769
                Address     000B.BE8A.CBC7
                Cost        19
                Port        4(FastEthernet0/4)
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
   
     Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                Address     00E0.8F5E.2769
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                Aging Time  20
   
   Interface        Role Sts Cost      Prio.Nbr Type
   ---------------- ---- --- --------- -------- --------------------------------
   Fa0/2            Altn BLK 19        128.2    P2p
   Fa0/4            Root FWD 19        128.4    P2p

#### Шаг 2.	Измените стоимость порта:

    S3(config-if)#int f0/4
    S3(config-if)#spanning-tree vlan 1 cost 18

#### Шаг 3.	Просмотрите изменения протокола spanning-tree:

  1. S2:

   S2#show spanning-tree 
   VLAN0001
     Spanning tree enabled protocol ieee
     Root ID    Priority    32769
                Address     000B.BE8A.CBC7
                Cost        19
                Port        2(FastEthernet0/2)
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
   
     Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                Address     0030.A3C5.8617
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                Aging Time  20
   
   Interface        Role Sts Cost      Prio.Nbr Type
   ---------------- ---- --- --------- -------- --------------------------------
   Fa0/2            Root FWD 19        128.2    P2p
   Fa0/4            Altn BLK 19        128.4    P2p

  2. S3:

   S3#show spanning-tree 
   VLAN0001
     Spanning tree enabled protocol ieee
     Root ID    Priority    32769
                Address     000B.BE8A.CBC7
                Cost        18
                Port        4(FastEthernet0/4)
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
   
     Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                Address     00E0.8F5E.2769
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                Aging Time  20
   
   Interface        Role Sts Cost      Prio.Nbr Type
   ---------------- ---- --- --------- -------- --------------------------------
   Fa0/2            Desg LSN 18        128.2    P2p
   Fa0/4            Root FWD 18        128.4    P2p

#### Шаг 4.	Удалите изменения стоимости порта:

    S3(config-if)#int f0/4
    S3(config-if)#no spanning-tree vlan 1 cost 18

##### Повторно выполните команду show spanning-tree, чтобы подтвердить, что протокол STP сбросил порт на коммутаторе некорневого моста, вернув исходные настройки порта. Протоколу STP требуется примерно 30 секунд, чтобы завершить процесс перевода порта:

   S3(config-if)#do show spanning-tree
   VLAN0001
     Spanning tree enabled protocol ieee
     Root ID    Priority    32769
                Address     000B.BE8A.CBC7
                Cost        19
                Port        4(FastEthernet0/4)
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
   
     Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                Address     00E0.8F5E.2769
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                Aging Time  20
   
   Interface        Role Sts Cost      Prio.Nbr Type
   ---------------- ---- --- --------- -------- --------------------------------
   Fa0/2            Altn BLK 19        128.2    P2p
   Fa0/4            Root FWD 19        128.4    P2p


### Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов.

  1. Включите порты F0/1 и F0/3 на всех коммутаторах:

    S1(config)#int range f0/1, f0/3
    S1(config-if-range)#no shutdown

  2. Подождите 30 секунд, чтобы протокол STP завершил процесс перевода порта, после чего выполните команду show spanning-tree на коммутаторах некорневого моста:

     2.1. S2:

   S2#show spanning-tree 
   VLAN0001
     Spanning tree enabled protocol ieee
     Root ID    Priority    32769
                Address     000B.BE8A.CBC7
                Cost        19
                Port        1(FastEthernet0/1)
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
   
     Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                Address     0030.A3C5.8617
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                Aging Time  20
   
   Interface        Role Sts Cost      Prio.Nbr Type
   ---------------- ---- --- --------- -------- --------------------------------
   Fa0/1            Root FWD 19        128.1    P2p
   Fa0/2            Altn BLK 19        128.2    P2p
   Fa0/4            Desg FWD 19        128.4    P2p
   Fa0/3            Desg FWD 19        128.3    P2p


     2.2. S3:

   S3#show spanning-tree 
   VLAN0001
     Spanning tree enabled protocol ieee
     Root ID    Priority    32769
                Address     000B.BE8A.CBC7
                Cost        19
                Port        3(FastEthernet0/3)
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
   
     Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                Address     00E0.8F5E.2769
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                Aging Time  20
   
   Interface        Role Sts Cost      Prio.Nbr Type
   ---------------- ---- --- --------- -------- --------------------------------
   Fa0/1            Altn BLK 18        128.1    P2p
   Fa0/2            Altn BLK 18        128.2    P2p
   Fa0/3            Root FWD 19        128.3    P2p
   Fa0/4            Altn BLK 19        128.4    P2p

##### Вопросы: 

  1. Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста - **S2: f0/1, S3: f0/3.**

  2. Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах - **Они подключены напрямую к корневому коммутатору и имеют наименьший MAC.**







