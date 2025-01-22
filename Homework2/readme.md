# Лабораторная работа. Просмотр таблицы MAC-адресов коммутатора
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/87548ee84b7c8a8f2e1cf37a473efd5e9220a2ad/photo/topology1.png)



## Таблица адресации 

| Устройство  | Интерфейс  | IP-адрес | Маска подсети |
| :------------ |:---------------:| -----:| -----:|
| S1      | VLAN 1 | 192.168.1.11 | 255.255.255.0 |
| S2      | VLAN 1 | 192.168.1.12 | 255.255.255.0 |
| PC-A    | NIC    | 192.168.1.1 | 255.255.255.0 |
| PC-B    | NIC    | 192.168.1.2 | 255.255.255.0 |


## Задачи:

### Часть 1. Создание и настройка сети

### Часть 2. Изучение таблицы МАС-адресов коммутатора


## Решение:

### Часть 1. Часть 1. Создание и настройка сети:

#### Шаг 1. Подключите сеть в соответствии с топологией

1.	Устанаввливаем подключение PC-A (порт FastEthernet0) к коммутатору S1 (порт FastEthernet0/1).   
2.  Устанаввливаем подключение PC-B (порт FastEthernet0) к коммутатору S2 (порт FastEthernet0/1).
3.  Устанаввливаем подключение между коммутаторами S1 (порт GigabitEthernet0/1) и S2 (порт GigabitEthernet0/1).

#### Шаг 2. Настройте узлы ПК:

1. Задаем параметры узлу PC-A в соответствии с таблицей адресации:

   ![ip_PCA](https://github.com/EfremovaOD/Otus_Homeworks/blob/c24281ff6885d9dbed39eb8a6538a395232f92c3/photo/ip_PCA.png)
   
2. Задаем параметры узлу PC-B в соответствии с таблицей адресации:

   ![ip_PCB](https://github.com/EfremovaOD/Otus_Homeworks/blob/c24281ff6885d9dbed39eb8a6538a395232f92c3/photo/ip_PCB.png)

#### Шаг 3. Выполните инициализацию и перезагрузку коммутаторов:

1. Устанаввливаем консольное подключение  к коммутатору S1 с PC-A

2. Вводим команду **enable** , чтобы войти в привилегированный режим EXEC
   
3. Удаляем файл загрузочной конфигурации при помощи команды **erase startup-config** :
   
        Switch# erase startup-config 
        Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]
        [OK]
        Erase of nvram: complete
        %SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
        Switch#
   
4. Перезагружаем коммутатор при помощи команды **reload** :

        Switch#reload 
        Proceed with reload? [confirm]
        C2960 Boot Loader (C2960-HBOOT-M) Version 12.2(25r)FX, RELEASE SOFTWARE (fc4)
        Cisco WS-C2960-24TT (RC32300) processor (revision C0) with 21039K bytes of memory.
        2960-24TT starting...
        Base ethernet MAC Address: 0090.2B20.79C7
        Xmodem file system is available.
        Initializing Flash...
        flashfs[0]: 1 files, 0 directories
        flashfs[0]: 0 orphaned files, 0 orphaned directories
        flashfs[0]: Total bytes: 64016384
        flashfs[0]: Bytes used: 4670455
        flashfs[0]: Bytes available: 59345929
        flashfs[0]: flashfs fsck took 1 seconds.
        ...done Initializing Flash.
        
        Boot Sector Filesystem (bs:) installed, fsid: 3
        Parameter Block Filesystem (pb:) installed, fsid: 4
        
        
        Loading "flash:/2960-lanbasek9-mz.150-2.SE4.bin"...
        ########################################################################## [OK]
        Smart Init is enabled
        smart init is sizing iomem
                  TYPE      MEMORY_REQ
                TOTAL:      0x00000000
        Rounded IOMEM up to: 0Mb.
        Using 6 percent iomem. [0Mb/512Mb]

              Restricted Rights Legend
        Use, duplication, or disclosure by the Government is
        subject to restrictions as set forth in subparagraph
        (c) of the Commercial Computer Software - Restricted
        Rights clause at FAR sec. 52.227-19 and subparagraph
        (c) (1) (ii) of the Rights in Technical Data and Computer
        Software clause at DFARS sec. 252.227-7013.
           cisco Systems, Inc.
           170 West Tasman Drive
           San Jose, California 95134-1706
        Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
        Technical Support: http://www.cisco.com/techsupport
        Copyright (c) 1986-2013 by Cisco Systems, Inc.
        Compiled Wed 26-Jun-13 02:49 by mnguyen
        Initializing flashfs...
        fsck: Disable shadow buffering due to heap fragmentation.
        flashfs[2]: 2 files, 1 directories
        flashfs[2]: 0 orphaned files, 0 orphaned directories
        flashfs[2]: Total bytes: 32514048
        flashfs[2]: Bytes used: 11952128
        flashfs[2]: Bytes available: 20561920
        flashfs[2]: flashfs fsck took 2 seconds.
        flashfs[2]: Initialization complete....done Initializing flashfs.
        Checking for Bootloader upgrade..
        Boot Loader upgrade not required (Stage 2)
        POST: CPU MIC register Tests : Begin
        POST: CPU MIC register Tests : End, Status Passed
        POST: PortASIC Memory Tests : Begin
        POST: PortASIC Memory Tests : End, Status Passed
        POST: CPU MIC interface Loopback Tests : Begin
        POST: CPU MIC interface Loopback Tests : End, Status Passed
        POST: PortASIC RingLoopback Tests : Begin
        POST: PortASIC RingLoopback Tests : End, Status Passed
        POST: PortASIC CAM Subsystem Tests : Begin
        POST: PortASIC CAM Subsystem Tests : End, Status Passed
        POST: PortASIC Port Loopback Tests : Begin
        POST: PortASIC Port Loopback Tests : End, Status Passed
        Waiting for Port download...Complete

        This product contains cryptographic features and is subject to United
        States and local country laws governing import, export, transfer and
        use. Delivery of Cisco cryptographic products does not imply
        third-party authority to import, export, distribute or use encryption.
        Importers, exporters, distributors and users are responsible for
        compliance with U.S. and local country laws. By using this product you
        agree to comply with applicable laws and regulations. If you are unable
        to comply with U.S. and local laws, return this product immediately.
        A summary of U.S. laws governing Cisco cryptographic products may be found at:
        http://www.cisco.com/wwl/export/crypto/tool/stqrg.html
        If you require further assistance please contact us by sending email to
        export@cisco.com.
        cisco WS-C2960-24TT-L (PowerPC405) processor (revision B0) with 65536K bytes of memory.
        Processor board ID FOC1010X104
        Last reset from power-on
        1 Virtual Ethernet interface
        24 FastEthernet interfaces
        2 Gigabit Ethernet interfaces
        The password-recovery mechanism is enabled.
        64K bytes of flash-simulated non-volatile configuration memory.
        Base ethernet MAC Address       : 00:90:2B:20:79:C7
        Motherboard assembly number     : 73-10390-03
        Power supply part number        : 341-0097-02
        Motherboard serial number       : FOC10093R12
        Power supply serial number      : AZS1007032H
        Model revision number           : B0
        Motherboard revision number     : B0
        Model number                    : WS-C2960-24TT-L
        System serial number            : FOC1010X104
        Top Assembly Part Number        : 800-27221-02
        Top Assembly Revision Number    : A0
        Version ID                      : V02
        CLEI Code Number                : COM3L00BRA
        Hardware Board Revision Number  : 0x01

        Switch Ports Model              SW Version            SW Image
        ------ ----- -----              ----------            ----------
        *    1 26    WS-C2960-24TT-L    15.0(2)SE4            C2960-LANBASEK9-M

        Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
        Technical Support: http://www.cisco.com/techsupport
        Copyright (c) 1986-2013 by Cisco Systems, Inc.
        Compiled Wed 26-Jun-13 02:49 by mnguyen
        
        

        Press RETURN to get started!
        
        
        %LINK-5-CHANGED: Interface FastEthernet0/1, changed state to up
        
        %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
        
        %LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up
        
        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
        
5. Устанавливаем консольное подключение  к коммутатору S2 с PC-B и проводим аналогичные действия.

#### Шаг 4. Настройте базовые параметры каждого коммутатора:

1. В режиме глобальной конфигурации назначаем базовые параметры конфигурации на коммутаторе S1:
   
       no ip domain-lookup
       hostname S1
       service password-encryption
       enable secret class
       banner motd #
       Unauthorized access is strictly prohibited. #

3. Назначаем ip-адрес:
   
       S1#conf t 
       S1(config)#interface vlan 1
       S1(config-if)#ip address 192.168.1.11 255.255.255.0
       S1(config-if)#no shutdown

4. Устанавливаем пароль на подключение через консольный кабель:
   
       S1(config)#line console 0
       S1(config-line)#password class
       S1(config-line)#login
       S1(config-line)#exit

5. Настраиваем VTY на подключение Telnet (с установкой пароля):
   
       S1(config)#line vty 0 4
       S1(config-line)#password class
       S1(config-line)#login
       S1(config-line)#transport input telnet. 


6. Сохраняем конфигурацию:

       S1#copy running-config startup-config 
       Destination filename [startup-config]? 
       Building configuration...
       [OK]
       S1#

7. Аналогичные настройки производим на коммутаторе S2:

       no ip domain-lookup
       hostname S1
       service password-encryption
       enable secret class
       banner motd #
       Unauthorized access is strictly prohibited. #
			
       S2#conf t 
       S2(config)#interface vlan 1
       S2(config-if)#ip address 192.168.1.12 255.255.255.0
       S2(config-if)#no shutdown
       
       S2(config)#line console 0
       S2(config-line)#password class
       S2(config-line)#login
       S2(config-line)#exit
       
       S2(config)#line vty 0 4
       S2(config-line)#password class
       S2(config-line)#login
       S2(config-line)#transport input telnet.
			
       S2#copy running-config startup-config 
       Destination filename [startup-config]? 
       Building configuration...
       [OK]
       S2#
   
### Часть 2. Изучение таблицы МАС-адресов коммутатора.

#### Шаг 1. Запишите МАС-адреса сетевых устройств:

1. Откройте командную строку на PC-A и PC-B и введите команду **ipconfig /all**:

	a. MAC узла PC-A:
 
		Physical Address................: 00E0.8FE9.44A1

	b. MAC узла PC-B:

		Physical Address................: 0005.5E04.02C3

2. Подключитесь к коммутаторам S1 и S2 через консоль и введите команду **show interface F0/1** на каждом коммутаторе:

   	a. MAC коммутатора S1:

		address is 000b.be58.8601 (bia 000b.be58.8601)

	b. MAC коммутатора S2:

		address is 0003.e4b6.a801 (bia 0003.e4b6.a801)


#### Шаг 2. Просмотрите таблицу МАС-адресов коммутатора:

1. Подключитесь к коммутатору S2 через консоль и войдите в привилегированный режим EXEC:

       C:\>telnet 192.168.1.12
       Trying 192.168.1.12 ...Open
       Unauthorized access is strictly prohibited. 
       
       
       User Access Verification
       
       Password: 
       S2>
       S2>en
       Password: 
       S2#

2. В привилегированном режиме EXEC введите команду **show mac address-table** и нажмите клавишу ввода:

       S2#show mac address-table
          Mac Address Table
       -------------------------------------------
       
       Vlan    Mac Address       Type        Ports
       ----    -----------       --------    -----
       
        1    0005.5e04.02c3    DYNAMIC     Fa0/1
        1    000b.be58.8619    DYNAMIC     Gig0/1   

#### Шаг 3. Очистите таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов:

1. В привилегированном режиме EXEC введите команду **clear mac address-table dynamic** и нажмите клавишу Enter:

       S2#clear mac address-table dynamic 

2. Снова быстро введите команду show mac address-table:

       S2#show mac address-table
          Mac Address Table
       -------------------------------------------
              
       Vlan    Mac Address       Type        Ports
       ----    -----------       --------    -----
       
         1    0005.5e04.02c3    DYNAMIC     Fa0/1

3. Проверяем результат через 10 сек :

       S2#show mac address-table
          Mac Address Table
       -------------------------------------------
       
       Vlan    Mac Address       Type        Ports
       ----    -----------       --------    -----
       
        1    0005.5e04.02c3    DYNAMIC     Fa0/1
        1    000b.be58.8619    DYNAMIC     Gig0/1 

  
#### Шаг 4. С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора:

1. На компьютере PC-B откройте командную строку и еще раз введите команду **arp -a**:


       C:\>arp -a 
       Internet Address      Physical Address      Type
       192.168.1.12          0002.176b.1ede        dynamic

2. Из командной строки PC-B отправьте эхо-запросы на компьютер PC-A, а также коммутаторы S1 и S2:

	a. Ping PC-A:

   ![ping pc-a](https://github.com/EfremovaOD/Otus_Homeworks/blob/b85e40043d0ae61e7d24db7fddb989bc648ef801/photo/Ping%20PC-A.png)
   
	b. Ping S1:

   ![ping S1](https://github.com/EfremovaOD/Otus_Homeworks/blob/b85e40043d0ae61e7d24db7fddb989bc648ef801/photo/Ping%20S1.png)
   
   	c. Ping S2:
    
   ![ping S2](https://github.com/EfremovaOD/Otus_Homeworks/blob/b85e40043d0ae61e7d24db7fddb989bc648ef801/photo/Ping%20S2.png)

4. Подключившись через консоль к коммутатору S2, введите команду **show mac address-table**:

       S2#show mac address-table 
                 Mac Address Table
       -------------------------------------------
       
       Vlan    Mac Address       Type        Ports 
       ----    -----------       --------    -----
       1    0005.5e04.02c3    DYNAMIC     Fa0/1    
       1    000b.be58.8619    DYNAMIC     Gig0/1
       1    0090.2b20.79c7    DYNAMIC     Gig0/1
       1    00e0.8fe9.44a1    DYNAMIC     Gig0/1

5. Повторяем запрос **arp -a** на узле PC-B: 

       C:\>arp -a 
       Internet Address      Physical Address      Type
       192.168.1.1           00e0.8fe9.44a1        dynamic
       192.168.1.11          0090.2b20.79c7        dynamic
       192.168.1.12          0002.176b.1ede        dynamic
