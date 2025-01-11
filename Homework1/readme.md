# Лабораторная работа. Базовая настройка коммутатора 
### Топология


### Таблица адресации 

| Устройство  | Интерфейс  |IP-адрес / префикс |
| :------------ |:---------------:| -----:|
| S1      | VLAN 1 | 192.168.1.2 /24 |
| PC-A      | NIC      | 192.168.1.1 /24 |

### Задачи:
##### Часть 1. Проверка конфигурации коммутатора по умолчанию
##### Часть 2. Создание сети и настройка основных параметров устройства 

- Настройте базовые параметры коммутатора
- Настройте IP-адрес для ПК

#####Часть 3. Проверка сетевых подключений
-	Отобразите конфигурацию устройства
-	Протестируйте сквозное соединение, отправив эхо-запрос
-	Протестируйте возможности удаленного управления с помощью Telnet

###Решение:
####Часть 1. Проверка конфигурации коммутатора по умолчанию:
#####Шаг 1. Создаем сеть согласно топологии
1.	Подсоединяем консольный кабель, как показано в топологии. 
2.  Устанаввливаем консольное подключение  к коммутатору с PC-A

#####Шаг 2. Проверяем настройки коммутатора по умолчанию
1. Вводим команду **enable**, чтобы войти в привилегированный режим EXEC
2. Убеждаемся, что на коммутаторе находится пустой файл конфигурации по умолчанию, с помощью команды** Show Running-Config**:
		> Switch#show running-config 
		Building configuration...
		Current configuration : 1080 bytes
		!
		version 15.0
		no service timestamps log datetime msec
		no service timestamps debug datetime msec
		no service password-encryption
		!
		hostname Switch
		!
		!
		!
		!
		!
		!
		spanning-tree mode pvst
		spanning-tree extend system-id
		!
		interface FastEthernet0/1
		!
		interface FastEthernet0/2
		!
		interface FastEthernet0/3
		!
		interface FastEthernet0/4
		!
		interface FastEthernet0/5
		!
		interface FastEthernet0/6
		!
		interface FastEthernet0/7
		!
		interface FastEthernet0/8
		!
		interface FastEthernet0/9
		!
		interface FastEthernet0/10
		!
		interface FastEthernet0/11
		!
		interface FastEthernet0/12
		!
		interface FastEthernet0/13
		!
		interface FastEthernet0/14
		!
		interface FastEthernet0/15
		!
		interface FastEthernet0/16
		!
		interface FastEthernet0/17
		!
		interface FastEthernet0/18
		!
		interface FastEthernet0/19
		!
		interface FastEthernet0/20
		!
		interface FastEthernet0/21
		!
		interface FastEthernet0/22
		!
		interface FastEthernet0/23
		!
		interface FastEthernet0/24
		!
		interface GigabitEthernet0/1
		!
		interface GigabitEthernet0/2
		!
		interface Vlan1
 		no ip address
		 shutdown
		!
		!
		!
		!
		line con 0
		!
		line vty 0 4
		 login
		line vty 5 15
		 login
		!
		!
		!
		!
		end

3.Проверяем наличие загрузочной конфигурации с помощью комманды **Show Startup-config**:
	> startup-config is not present
4.Изучаем характеристики SVI для VLAN 1:
	>VLAN 1 не настроен и не имеет ip-адреса

5.Изучаем сведения о версии ОС Cisco IOS на коммутаторе:
	> 15.0(2)SE4
	> Файл образа: C2960-LANBASEK9-M
6.Изучите свойства по умолчанию интерфейса FastEthernet, который используется компьютером PC-A:
	>Интерфейс выключен и имеет стандартные настройки 
####Часть 2. Настройка базовых параметров сетевых устройств:
#####Шаг 1. Настройте базовые параметры коммутатора

1.	В режиме глобальной конфигурации копируем следующие базовые параметры конфигурации и вставляем их в файл на коммутаторе S1:
			> no ip domain-lookup
			>hostname S1
			>service password-encryption
			>enable secret class
			>banner motd #
			>Unauthorized access is strictly prohibited. #

2. Назначаем ip-адрес:
		S1#conf t 
		S1(config)#interface vlan 1
		S1(config-if)#ip address 192.168.1.2 255.255.255.0
		S1(config-if)#no shutdown
3. Устанавливаем пароль на подключение через консольный кабель:
		S1(config)#line console 0
		S1(config-line)#password class
		S1(config-line)#login
		S1(config-line)#end
4. Настраиваем VTY на подключение Telnet(с установкой пароля):
		S1(config)#line vtyб
		S1(config-line)#password class
		S1(config-line)#login
		S1(config-line)#transport input telnet
#####Шаг 2. Настройте IP-адрес на компьютере PC-A
1. Назначаем PC-A ip-адресс: 192.168.1.1, маска: 255.255.255.0

####Часть 3. Проверка сетевых подключений
#####Шаг 1. Отобразите конфигурацию коммутатора
		S1#show run
		Building configuration...
		
		Current configuration : 1342 bytes
		!
		version 15.0
		no service timestamps log datetime msec
				no service timestamps debug datetime msec
		service password-encryption
		!
		hostname S1
		!
		enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
		!
		!
		!
		no ip domain-lookup
		!
		!
		!
		spanning-tree mode pvst
		spanning-tree extend system-id
		!
		interface FastEthernet0/1
		!
		interface FastEthernet0/2
		!
		interface FastEthernet0/3
		!
		interface FastEthernet0/4
		!
		interface FastEthernet0/5
		!
		interface FastEthernet0/6
		!
		interface FastEthernet0/7
		!
		interface FastEthernet0/8
		!
		interface FastEthernet0/9
		!
		interface FastEthernet0/10
		!
		interface FastEthernet0/11
		!
		interface FastEthernet0/12
		!
		interface FastEthernet0/13
		!
		interface FastEthernet0/14
		!
		interface FastEthernet0/15
		!
		interface FastEthernet0/16
		!
		interface FastEthernet0/17
		!
		interface FastEthernet0/18
		!
		interface FastEthernet0/19
		!
		interface FastEthernet0/20
		!
		interface FastEthernet0/21
		!
		nterface FastEthernet0/22
		!
		interface FastEthernet0/23
		!
		interface FastEthernet0/24
		!
		interface GigabitEthernet0/1
		!
		interface GigabitEthernet0/2
		!
		interface Vlan1
 		ip address 192.168.1.2 255.255.255.0
		!
		banner motd ^C
		Unauthorized access is strictly prohibited. ^C
		!
		!
		!
		line con 0
		 password 7 0822404F1A0A
		 logging synchronous
		 login
				!
		line vty 0 4
		 password 7 0822404F1A0A
		 login
				 transport input telnet
		line vty 5 15
 		password 7 0822404F1A0A
		 login
		!
		!
		!
		!
		end
#####Шаг 2. Протестируйте сквозное соединение, отправив эхо-запрос

1.Проверяем доступность PC-A:

		ping 192.168.1.1

2.Направляем Эхо-запрос на S1:

		ping 192.168.1.2

#####Шаг 3. Проверьте удаленное управление коммутатором S1
1. 




