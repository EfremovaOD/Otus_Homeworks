# Лабораторная работа - Настройка NAT для IPv4.
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/47009ba04141db1e12a181f2bfa9189a5d9d4602/photo/topology13.PNG)

## Таблица адресации 

| Устройство  | Интерфейс  | IPv4-адрес | Маска подсети | Шлюз по умолчанию |
| :------ |:------------:| --------:| ---------------:| ---------------:|
| R1      | Loopback1  | 172.16.1.1 | 255.255.255.0 | - |
| R1      | G0/0/1     | 10.22.0.1  | 255.255.255.0 | - |
| S1      | SVI VLAN 1 | 10.22.0.2  | 255.255.255.0 | 10.22.0.1 |
| S2      | SVI VLAN 1 | 10.22.0.3  | 255.255.255.0 | 10.22.0.1 |


## Задачи:

### Часть 1. Создание сети и настройка основных параметров устройства.

### Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP.

### Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP.

### Часть 4. Настройка и проверка NTP.

## Решение:

### Часть 1. Создание сети и настройка основных параметров устройства.

#### Шаг 1. Создание сети:

1. Создаем сеть согласно топологии. (см. Топология)

#### Шаг 2. Настройка и проверка основных параметров маршрутизатора:

1. Назначьте маршрутизатору имя устройства.
2. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
3. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
4. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
5. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю. f. Зашифруйте открытые пароли.
6. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
7. Настройка интерфейсов, перечисленных в таблице выше.
8. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

#### Шаг 3. Настройка и проверка основных параметров коммутаторов:

1. Присвойте коммутатору имя устройства.
2. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
3. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
4. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
5. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю. f. Зашифруйте открытые пароли.
6. Создайте баннер, который предупреждает всех, кто обращается к устройству, видит баннерное сообщение «Только авторизованные пользователи!».
7. Отключите неиспользуемые интерфейсы.
8. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

### Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP.

*На устройствах Cisco протокол CDP включен по умолчанию. Воспользуйтесь CDP, чтобы обнаружить порты, к которым подключены кабели.*

1. На R1 используйте соответствующую команду **show cdp**, чтобы определить, сколько интерфейсов включено CDP, сколько из них включено и сколько отключено.

    R1#show cdp interface
     Vlan1 is administratively down, line protocol is down
          Sending CDP packets every 60 seconds
          Holdtime is 180 seconds
     GigabitEthernet0/0/0 is administratively down, line protocol is down
          Sending CDP packets every 60 seconds
          Holdtime is 180 seconds
     GigabitEthernet0/0/1 is up, line protocol is up
          Sending CDP packets every 60 seconds
          Holdtime is 180 seconds
     GigabitEthernet0/0/2 is administratively down, line protocol is down
          Sending CDP packets every 60 seconds
          Holdtime is 180 seconds

**Сколько интерфейсов участвует в объявлениях CDP?**  *Интерфейсы: VLAN1, G0/0/0, G0/0/1, G0/0/2*

**Какие из них активны?** *GigabitEthernet0/0/1*

2. На R1 используйте соответствующую команду **show cdp entry S1**, чтобы определить информацию о соседних сетевых устройствах.

    R1#show cdp entry S1
    
     Device ID: S1
     Entry address(es): 
     Platform: cisco 2960, Capabilities: Switch
     Interface: GigabitEthernet0/0/1, Port ID (outgoing port): FastEthernet0/5
     Holdtime: 163
    
     Version :
     Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
     Technical Support: http://www.cisco.com/techsupport
     Copyright (c) 1986-2013 by Cisco Systems, Inc.
     Compiled Wed 26-Jun-13 02:49 by mnguyen
    
     advertisement version: 2
     Duplex: full

**Какая версия IOS используется на S1?** *Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)*

3.	На S1 используйте соответствующую команду **show cdp traffic**, чтобы определить, сколько пакетов CDP было выданных.

*CPT, увы, не поддерживает данную команду. Так что это останется тайной*

    S1#show cdp traffic
               ^
    % Invalid input detected at '^' marker.

4.	Настройте SVI для VLAN 1 на S1 и S2, используя IP-адреса, указанные в таблице адресации выше. Настройте шлюз по умолчанию для каждого коммутатора на основе таблицы адресов. 
5.	На R1 выполните команду **show cdp entry S1**:

    R1#show cdp entry S1
     Device ID: S1
     Entry address(es): 
          IP address : 10.22.0.2

6.	Отключить CDP глобально на всех устройствах:

    R1(config)#no cdp run

  *Проверим:*

    R1#sh cdp
    % CDP is not enabled
    R1#

### Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP.

1.	Введите соответствующую команду lldp, чтобы включить LLDP на всех устройствах в топологии:

    R1(config)#lldp run
  
2.	На S1 выполните соответствующую команду lldp, чтобы предоставить подробную информацию о S2:

   *Для получения подробной информации о S2 на S1 - необходимо выполнить команду* **show lldp entry S2** *, но CPT её не поддерживает, используем команду* **show lldp neighbors detail**

    S1#show lldp neighbors detail 
     ------------------------------------------------
     Chassis id: 0006.2A81.4601
     Port id: Fa0/1
     Port Description: FastEthernet0/1
     System Name: S2
     System Description:
     Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
     Technical Support: http://www.cisco.com/techsupport
     Copyright (c) 1986-2013 by Cisco Systems, Inc.
     Compiled Wed 26-Jun-13 02:49 by mnguyen
     Time remaining: 90 seconds
     System Capabilities: B
     Enabled Capabilities: B
     Management Addresses - not advertised
     Auto Negotiation - supported, enabled
     Physical media capabilities:
     100baseT(FD)
     100baseT(HD)
     1000baseT(HD)
     Media Attachment Unit type: 10
     Vlan ID: 1

3.	Соединитесь через консоль на всех устройствах и используйте команды **LLDP**, необходимые для отображения топологии физической сети только из выходных данных команды **show**.

##### R1
    R1#sh lldp neighbors 
     Capability codes:
     (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
     (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
     Device ID           Local Intf     Hold-time  Capability      Port ID
     S1                  Gig0/0/1       120        B               Fa0/5
    
     Total entries displayed: 1

##### S1

     S1#show lldp neighbors 
     Capability codes:
     (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
     (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
     Device ID           Local Intf     Hold-time  Capability      Port ID
     S2                  Fa0/1          120        B               Fa0/1
     R1                  Fa0/5          120        R               Gig0/0/1

     Total entries displayed: 2

##### S2
    
     S2#sh lldp neighbors 
     Capability codes:
     (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
     (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
     Device ID           Local Intf     Hold-time  Capability      Port ID
     S1                  Fa0/1          120        B               Fa0/1
    
     Total entries displayed: 1

### Часть 4. Настройка NTP.

#### Шаг 1. Выведите на экран текущее время:

1. Введите команду **show clock** для отображения текущего времени на R1. Запишите отображаемые сведения о текущем времени в следующей таблице.

     R1#sh clock detail 
     *0:1:24.789 UTC Mon Mar 1 1993
     Time source is hardware calendar
     R1#

| Дата  | Время  | Часовой пояс | Источник времени |
| :------ |:------------:| --------:| ---------------:|
| Mar 1 1993 | 0:1:24.789 | UTC |  Time source is hardware calendar |

#### Шаг 2. Установите время:

     R1#clock set 10:48:00 17 June 2025
     R1#sh clock 
     10:48:7.184 UTC Tue Jun 17 2025

#### Шаг 3. Настройте главный сервер NTP:

     R1(config)#ntp master 4

#### Шаг 4. Настройте клиент NTP:

1.	Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время. Запишите текущее время,  в следующей таблице:


| Устройство  | Дата  | Время  | Часовой пояс |
| :---- |:------:| ------------:| ------------:|
| S1 | Mar 1 1993 | 0:3:19.789 | UTC |
| S2 | Mar 1 1993 | 0:3:24.789 | UTC |

2.	Настройте S1 и S2 в качестве клиентов NTP. Используйте соответствующие команды NTP для получения времени от интерфейса G0/0/1 R1, а также для периодического обновления календаря или аппаратных часов коммутатора:

     S1(config)#ntp server 10.22.0.1
     S2(config)#ntp server 10.22.0.1

#### Шаг 5. Проверьте настройку NTP.

1.	Используйте соответствующую команду **show** , чтобы убедиться, что S1 и S2 синхронизированы с R1:

      S2#show ntp associations 
     
      address         ref clock       st   when     poll    reach  delay          offset            disp
      *~10.22.0.1     127.127.1.1     4    13       16      217    0.00           0.00              0.12
      * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured

2.	Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время и сравнить ранее записанное время:

      S1#show clock detail
      11:35:35.155 UTC Tue Jun 17 2025
      Time source is NTP

      S2#show clock detail
      11:36:50.234 UTC Tue Jun 17 2025
      Time source is NTP

