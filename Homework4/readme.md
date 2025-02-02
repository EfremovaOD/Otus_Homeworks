# Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах
## Топология

![topology](https://github.com/EfremovaOD/Otus_Homeworks/blob/a82566b37cab86939f1904f077adea9e3bdf1b87/photo/topology.png)



## Таблица адресации 

| Устройство  | Интерфейс  | IPv6-адрес | Link local IPv6-адрес | Длина префикса | Шлюз по умолчанию |
| :------------ |:---------------:| :-----:| :-----:| :-----:| :-----:|
| R1     | G0/0/0 | 2001:db8:acad:a::1 | fe80::1 | 64 | - |
| R1     | G0/0/1 | 2001:db8:acad:1::1 | fe80::1 | 64 | - |
| S1     | VLAN 1 | 2001:db8:acad:1::b | fe80::b | 64 | - |
| PC-A   | NIC    | 2001:db8:acad:1::3 | SLACC   | 64 | fe80::1 |
| PC-B   | NIC    | 2001:db8:acad:a::3 | SLACC   | 64 | fe80::1 |

## Задачи:

### Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

### Часть 2. Ручная настройка IPv6-адресов 

### Часть 3. Проверка сквозного соединения
-------------

## Решение:

### Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора:

#### Шаг 1. Настройте маршрутизатор:

1. Назначьте имя хоста и настройте основные параметры устройства:
	
 	a. В режиме глобальной конфигурации назначаем базовые параметры конфигурации на маршрутизаторе R1:
   
       no ip domain-lookup
       hostname R1
       service password-encryption
       enable secret class
       banner motd #
       Unauthorized access is strictly prohibited. #
     

#### Шаг 2. Настройте коммутатор:

1. Назначьте имя хоста и настройте основные параметры устройства:
	
 	a. В режиме глобальной конфигурации назначаем базовые параметры конфигурации на коммутаторе S1:
   
       no ip domain-lookup
       hostname S1
       service password-encryption
       enable secret class
       banner motd #
       Unauthorized access is strictly prohibited. #
   
	b. Для включения IPv6-адресации выполняем следующие действия:

       S1# configure terminal
       S1(config)# sdm prefer dual-ipv4-and-ipv6 default
       S1(config)# end
       S1# reload


### Часть 2. Ручная настройка IPv6-адресов:

#### Шаг 1. Назначьте IPv6-адреса интерфейсам Ethernet на R1:

1. Назначьте глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1:

	a. Настроим интерфейс G0/0/0:

       R1>   
       R1>en
       Password: 
       R1#conf t
       Enter configuration commands, one per line.  End with CNTL/Z.
       R1(config)#
       R1(config)#int g0/0/0
       R1(config-if)#no shutdown
       R1(config-if)#ipv6 enable
       R1(config-if)#ipv6 address 2001:db8:acad:a::1/64

	b. Настроим интерфейс G0/0/1:

       R1(config)#int g0/0/1
       R1(config-if)#no shutdown
       R1(config-if)#ipv6 enable
       R1(config-if)#ipv6 address 2001:db8:acad:1::1/64

3. Введите команду **show ipv6 interface brief**, чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес:


      	R1#show ipv6 int brief
      	GigabitEthernet0/0/0       [up/up]
      	FE80::205:5EFF:FEA0:3A01
      	2001:DB8:ACAD:A::1
      	GigabitEthernet0/0/1       [up/up]
      	FE80::205:5EFF:FEA0:3A02
      	2001:DB8:ACAD:1::1
      	GigabitEthernet0/0/2       [administratively down/down]
      	unassigned
      	Vlan1                      [administratively down/down]
      	unassigned
      	 R1#

4. Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную введите локальные адреса канала на каждом интерфейсе Ethernet на R1:

       R1(config)#int g0/0/0
       R1(config-if)#ipv6 address fe80::1 link-local
       R1(config-if)#end
       R1(config)#int g0/0/1
       R1(config-if)#ipv6 address fe80::1 link-local

5. Используйте выбранную команду, чтобы убедиться, что локальный адрес связи изменен на fe80::1 :

       R1#show ipv6 interface brief
       GigabitEthernet0/0/0       [up/up]
        FE80::1
       2001:DB8:ACAD:A::1
       GigabitEthernet0/0/1       [up/up]
       FE80::1
       2001:DB8:ACAD:1::1
       GigabitEthernet0/0/2       [administratively down/down]
       unassigned
       Vlan1                      [administratively down/down]
       unassigned
       R1#

#### Шаг 2. Активируйте IPv6-маршрутизацию на R1:

1. В командной строке на PC-B введите команду **ipconfig**, чтобы получить данные IPv6-адреса, назначенного интерфейсу ПК:
   
      	C:\>ipconfig
      	
      	FastEthernet0 Connection:(default port)
      	
      	Connection-specific DNS Suffix..: 
      	 Link-local IPv6 Address.........: FE80::230:F2FF:FE71:ED61
      	IPv6 Address....................: ::
         IPv4 Address....................: 0.0.0.0
      	Subnet Mask.....................: 0.0.0.0
      	 Default Gateway.................: ::
      		                           0.0.0.0
			

2. Активируйте IPv6-маршрутизацию на R1 с помощью команды **IPv6 unicast-routing**:

      	R1#conf t
      	Enter configuration commands, one per line.  End with CNTL/Z.
      	R1(config)# IPv6 unicast-routing
      	R1(config)#

3. Теперь, когда R1 входит в группу многоадресной рассылки всех маршрутизаторов, еще раз введите команду ipconfig на PC-B. Проверьте данные IPv6-адреса:

      	C:\>ipconfig
      	      	
      	FastEthernet0 Connection:(default port)
      	
      	Connection-specific DNS Suffix..: 
      	Link-local IPv6 Address.........: FE80::230:F2FF:FE71:ED6
      	IPv6 Address....................: 2001:DB8:ACAD:1:230:F2FF:FE71:ED6
      	IPv4 Address....................: 0.0.0.0
      	Subnet Mask.....................: 0.0.0.0
      	Default Gateway.................: FE80::1
                                          0.0.0.0

#### Шаг 3. Назначьте IPv6-адреса интерфейсу управления (SVI) на S1:

1. Назначьте адрес IPv6 для S1. Также назначьте этому интерфейсу локальный адрес канала fe80::b :
   
      	S1>
      	S1>en
      	Password: 
      	S1#
      	S1#conf t
      	Enter configuration commands, one per line.  End with CNTL/Z.
      	S1(config)#
      	S1(config)#int vlan1
      	S1(config-if)#no shutdown
      	S1(config-if)#ipv6 address 2001:db8:acad:1::b/64
      	S1(config-if)#
      	S1(config-if)#ipv6 address fe80::b link-local 
      	S1(config-if)#end
      	S1(config)#IPv6 unicast-routing		

3. Проверьте правильность назначения IPv6-адресов интерфейсу управления с помощью команды **show ipv6 interface vlan1**:

      	S1#show ipv6 interface vlan 1
      	Vlan1 is up, line protocol is up
      	  IPv6 is enabled, link-local address is FE80::B
      	  No Virtual link-local address(es):
      	  Global unicast address(es):
      	    2001:DB8:ACAD:1::B, subnet is 2001:DB8:ACAD:1::/64
      	  Joined group address(es):
      	    FF02::1
      	    FF02::1:FF00:B
      	  MTU is 1500 bytes
      	  ICMP error messages limited to one every 100 milliseconds
      	  ICMP redirects are enabled
      	  ICMP unreachables are sent
      	  Output features: Check hwidb
      	  ND DAD is enabled, number of DAD attempts: 1
      	  ND reachable time is 30000 milliseconds
      	S1#

#### Шаг 4. Назначьте компьютерам статические IPv6-адреса:

1. Откройте окно Свойства Ethernet для каждого ПК и назначьте адресацию IPv6 :

	 PC-A:
![ip_PCA](https://github.com/EfremovaOD/Otus_Homeworks/blob/c24281ff6885d9dbed39eb8a6538a395232f92c3/photo/ip_PCA.png)

	 PC-B:
 ![ip_PCA](https://github.com/EfremovaOD/Otus_Homeworks/blob/c24281ff6885d9dbed39eb8a6538a395232f92c3/photo/ip_PCA.png)


### Часть 3. Проверка сквозного подключения:

1. С PC-A отправьте эхо-запрос на FE80::1. Это локальный адрес канала, назначенный G0/1 на R1:
   
			 no ip domain-lookup			

2. Отправьте эхо-запрос на интерфейс управления S1 с PC-A:

			 no ip domain-lookup

3. Введите команду tracert на PC-A, чтобы проверить наличие сквозного подключения к PC-B:

			 no ip domain-lookup

4. С PC-B отправьте эхо-запрос на PC-A:

			 no ip domain-lookup

5. С PC-B отправьте эхо-запрос на локальный адрес канала G0/0 на R1:

			 no ip domain-lookup
