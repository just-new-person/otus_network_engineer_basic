# Лабораторная работа. Настройка и проверка расширенных списков контроля доступа

### Топология
![](Топология_30.png)

### Таблица адресации

|	Устройство	|	Интерфейс	|	IP-адрес  |	Маска подсети |	Шлюз по умолчанию	|
|	---	|	---	|	---		|	---	|	---	|
|	R1	|	G0/0/1  |	—	|	—	|	—	|
|	R1	|	G0/0/1.20	|	10.20.0.1	|	255.255.255.0	|	—	|
|	R1	|	G0/0/1.30	|	10.30.0.1	|	255.255.255.0	|	—	|
|	R1	|	G0/0/1.40	|	10.40.0.1	|	255.255.255.0	|	—	|
|	R1	|	G0/0/1.1000	|	—	|	—	|	—	|
|	R1	|	Loopback1	|	172.16.1.1	|	255.255.255.0	|	—	|
|	R2	|	G0/0/1	|	10.20.0.4	|	255.255.255.0	|	—	|
|	S1	|	VLAN 20	|	10.20.0.2	|	255.255.255.0	|	10.20.0.1	|
|	S2	|	VLAN 20	|	10.20.0.3	|	255.255.255.0	|	10.20.0.1	|
|	PC-A	|	NIC	|	10.30.0.10	|	255.255.255.0	|	10.30.0.1	|
|	PC-B	|	NIC	|	10.40.0.10	|	255.255.255.0	|	10.40.0.1	|

### Таблица VLAN

|	VLAN	|	Имя	|	Назначенный интерфейс	|
|	---	|	---	|	---	|
|	20	|	Management	|	S2: F0/5	|
|	30	|	Operations	|	S1: F0/6	|
|	40	|	Sales	|	S2: F0/18	|
|	999	|	ParkingLot	|	S1: F0/2-4, F0/7-24, G0/1-2	|
|	1000	|	Собственная	|	S2: F0/2-4, F0/6-17, F0/19-24, G0/1-2	|

### Задачи
## Часть 1. Создание сети и настройка основных параметров устройства
## Часть 2. Настройка и проверка списков расширенного контроля доступа
	Общие сведения и сценарий
Вам было поручено настроить списки контроля доступа в сети небольшой компании. ACL являются одним из самых простых и прямых средств управления трафиком уровня 3. R1 будет размещать интернет-соединение (смоделированное интерфейсом Loopback 1) и предоставлять информацию о маршруте по умолчанию для R2. После завершения первоначальной настройки компания имеет некоторые конкретные требования к безопасности дорожного движения, которые вы несете ответственность за реализацию.
Примечание: Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.
Примечание. Убедитесь, что у всех маршрутизаторов и коммутаторов была удалена начальная конфигурация. Если вы не уверены в этом, обратитесь к инструктору.
 
	Необходимые ресурсы
•	2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
•	2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
•	2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
•	Консольные кабели для настройки устройств Cisco IOS через консольные порты.
•	Кабели Ethernet, расположенные в соответствии с топологией

Инструкции
## Часть 1. Создание сети и настройка основных параметров устройства
### Шаг 1. Создайте сеть согласно топологии.
![](Топология_30_вып.png)

### Шаг 2. Произведите базовую настройку маршрутизаторов.
Выполнено
#### a.	Назначьте маршрутизатору имя устройства.
#### b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
#### c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
#### d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
#### e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
#### f.	Зашифруйте открытые пароли.
#### g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
#### h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.


### Шаг 3. Настройте базовые параметры каждого коммутатора.
Выполнено
#### a.	Присвойте коммутатору имя устройства.
#### b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
#### c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
#### d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
#### e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
#### f.	Зашифруйте открытые пароли.
#### g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
#### h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.


## Часть 2. Настройка сетей VLAN на коммутаторах.
### Шаг 1. Создайте сети VLAN на коммутаторах.
#### a.	Создайте необходимые VLAN и назовите их на каждом коммутаторе из приведенной выше таблицы.
Настройка S2 по аналогии
```
S1(config)#vlan 20
S1(config-vlan)#name Management
S1(config-vlan)#vlan 30
S1(config-vlan)#name Operations
S1(config-vlan)#vlan 40
S1(config-vlan)#name Sales
S1(config-vlan)#vlan 999
S1(config-vlan)#name ParkingLot
S1(config-vlan)#vlan 1000
S1(config-vlan)#name Other
S1(config-vlan)#
```
#### b.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации.
S1
```
S1(config)#int vlan 20
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan20, changed state to up
S1(config-if)#ip address 10.20.0.2 255.255.255.0
S1(config-if)#ex
S1(config)#ip default-gateway 10.20.0.1
```
S2
```
S2(config)#int vlan 20
S2(config-if)#
%LINK-5-CHANGED: Interface Vlan20, changed state to up
S2(config-if)#ip address 10.20.0.3 255.255.255.0
S2(config-if)#ex
S2(config)#ip default-gateway 10.20.0.1
```
#### c.	Назначьте все неиспользуемые порты коммутатора VLAN Parking Lot, настройте их для статического режима доступа и административно деактивируйте их.
S1
```
S1(config)#int range f0/2-4,f0/7-24,g0/1-2
S1(config-if-range)#switchport mode access 
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shutdown
```
S2
```
S2(config)#int range f0/2-4,f0/6-17,f0/19-24,g0/1-2
S2(config-if-range)#switchport mode access 
S2(config-if-range)#switchport access vlan 999
S2(config-if-range)#shutdown
```

### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.
#### a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
S1
```
S1(config)#int f0/6
S1(config-if)#switchport mode access 
S1(config-if)#switchport access vlan 30
```
S2
```
S2(config)#int f0/5
S2(config-if)#switchport mode access 
S2(config-if)#switchport access vlan 20
S2(config-if)#int f0/18
S2(config-if)#switchport mode access 
S2(config-if)#switchport access vlan 40
```
#### b.	Выполните команду show vlan brief, чтобы убедиться, что сети VLAN назначены правильным интерфейсам.
S1
```
S1#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/5
20   Management                       active    
30   Operations                       active    Fa0/6
40   Sales                            active    
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1000 Other                            active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```
S2
```
S2#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1
20   Management                       active    Fa0/5
30   Operations                       active    
40   Sales                            active    Fa0/18
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1000 Other                            active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

## Часть 3. ·Настройте транки (магистральные каналы).
### Шаг 1. Вручную настройте магистральный интерфейс F0/1.
#### a.	Измените режим порта коммутатора на интерфейсе F0/1, чтобы принудительно создать магистральную связь. Не забудьте сделать это на обоих коммутаторах.
S1
```
S1(config)#int f0/1
S1(config-if)#switchport mode trunk 
S1(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan20, changed state to up
```
S2
```
S2(config)#int f0/1
S2(config-if)#switchport mode trunk 
S2(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan20, changed state to up
```
#### b.	В рамках конфигурации транка установите для native vlan значение 1000 на обоих коммутаторах. При настройке двух интерфейсов для разных собственных VLAN сообщения об ошибках могут отображаться временно.
Настройка S2 по аналогии
```
S1(config-if)#switchport trunk native vlan 1000
```
#### c.	В качестве другой части конфигурации транка укажите, что VLAN 20, 30, 40 и 1000 разрешены в транке.
Настройка S2 по аналогии
```
S1(config-if)#switchport trunk allowed vlan 20,30,40,1000
```
#### d.	Выполните команду show interfaces trunk для проверки портов магистрали, собственной VLAN и разрешенных VLAN через магистраль.
S1
```
S1#sh int trunk 
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30,40,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30,40,1000
```
S2
```
S2#sh int tr
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30,40,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30,40,1000
```

### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.
#### a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.
```
S1(config)#int f0/5
S1(config-if)#switchport mode trunk 
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#switchport trunk allowe vlan 20,30,40,1000
```
#### b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
#### c.	Используйте команду show interfaces trunk для проверки настроек транка.
Без включения порта g0/0/1 на R1 команда ничего не показывает. Забегая вперед указаний методички включаем порт.
```
S1#sh int trunk 
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000
Fa0/5       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000
Fa0/5       20,30,40,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30,40,1000
Fa0/5       20,30,40,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30,40,1000
Fa0/5       none
```

## Часть 4. Настройте маршрутизацию.
### Шаг 1. Настройка маршрутизации между сетями VLAN на R1.
#### a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.
```
R1#conf t
R1(config)#no ip domain-lookup 
R1(config)#int g0/0/1
R1(config-if)#no shutdown
```
#### b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейс для собственной VLAN не имеет назначенного IP-адреса. Включите описание для каждого подинтерфейса.
```
R1(config)#int g0/0/1.20
%LINK-3-UPDOWN: Interface GigabitEthernet0/0/1.20, changed state to down
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.20, changed state to up
R1(config-subif)#description subint vlan20 to S1 f0/5
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip address 10.20.0.1 255.255.255.0
R1(config-subif)#int g0/0/1.30
R1(config-subif)#description subint vlan30 to S1 f0/5
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip address 10.30.0.1 255.255.255.0
R1(config-subif)#int g0/0/1.40
R1(config-subif)#description subint vlan40 to S1 f0/5
R1(config-subif)#encapsulation dot1Q 40
R1(config-subif)#ip address 10.40.0.1 255.255.255.0
R1(config-subif)#int g0/0/1.1000
R1(config-subif)#description subint vla1000 to S1 f0/5
```
#### c.	Настройте интерфейс Loopback 1 на R1 с адресацией из приведенной выше таблицы.
```
R1(config)#int loopback 1

R1(config-if)#
%LINK-3-UPDOWN: Interface Loopback1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up

R1(config-if)#ip address 172.16.1.1 255.255.255.0
```
#### d.	С помощью команды show ip interface brief проверьте конфигурацию подынтерфейса.
```
R1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
GigabitEthernet0/0/1   unassigned      YES unset  up                    up 
GigabitEthernet0/0/1.2010.20.0.1       YES manual up                    up 
GigabitEthernet0/0/1.3010.30.0.1       YES manual up                    up 
GigabitEthernet0/0/1.4010.40.0.1       YES manual up                    up 
GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Loopback1              172.16.1.1      YES manual up                    up 
Vlan1                  unassigned      YES unset  administratively down down
```

### Шаг 2. Настройка интерфейса R2 g0/0/1 с использованием адреса из таблицы и маршрута по умолчанию с адресом следующего перехода 10.20.0.1
```
R2#conf t
R2(config)#int g0/0/1
R2(config-if)#no shutdown
R2(config-if)#ip address 10.20.0.4 255.255.255.0
R2(config)#ip route 0.0.0.0 0.0.0.0 10.20.0.1
```

## Часть 5. Настройте удаленный доступ
### Шаг 1. Настройте все сетевые устройства для базовой поддержки SSH.
#### a.	Создайте локального пользователя с именем пользователя SSHadmin и зашифрованным паролем $cisco123!
#### b.	Используйте ccna-lab.com в качестве доменного имени.
#### c.	Генерируйте криптоключи с помощью 1024 битного модуля.
#### d.	Настройте первые пять линий VTY на каждом устройстве, чтобы поддерживать только SSH-соединения и с локальной аутентификацией.
Настройка на R1, S1, S2 аналогична
```
R2(config)#ip domain name ccna-lab.com
R2(config)#crypto key generate rsa
The name for the keys will be: R2.ccna-lab.com
Choose the size of the key modulus in the range of 360 to 4096 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.
How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 1:22:59.615: %SSH-5-ENABLED: SSH 1.99 has been enabled
R2(config)#username SSHadmin privilege 15 secret $cisco123!
R2(config)#line vty 0 4
R2(config-line)#login local
R2(config-line)#transport input ssh
R2(config-line)#exit
R2(config)#ip ssh version 2
```

### Шаг 2. Включите защищенные веб-службы с проверкой подлинности на R1.
#### a.	Включите сервер HTTPS на R1.
Команда ip http secure-server CPT не принимает.
```
R1(config)#ip http secure-server
               ^
% Invalid input detected at '^' marker.
```
Как минимум на Маршрутизаторе ISR4331 нет всей ветки http. Интернет говорит что модель 4331 сильно урезана и в ней нет "ip http"
```
R1(config)#ip ?
  access-list       Named access-list
  cef               Cisco Express Forwarding
  default-gateway   Specify default gateway (if not routing IP)
  default-network   Flags networks as candidates for default routes
  dhcp              Configure DHCP server and relay parameters
  domain            IP DNS Resolver
  domain-lookup     Enable IP Domain Name System hostname translation
  domain-name       Define the default domain name
  flow-export       Specify host/port to send flow statistics
  forward-protocol  Controls forwarding of physical and directed IP broadcasts
  ftp               FTP configuration commands
  host              Add an entry to the ip hostname table
  inspect           Context-based Access Control Engine
  ips               Intrusion Prevention System
  local             Specify local options
  name-server       Specify address of name server to use
  nat               NAT configuration commands
  route             Establish static routes
  routing           Enable IP routing
  scp               Scp commands
  ssh               Configure ssh options
  tcp               Global TCP parameters
```
#### b.	Настройте R1 для проверки подлинности пользователей, пытающихся подключиться к веб-серверу.
R1(config)# ip http authentication local


## Часть 6. Проверка подключения
### Шаг 1. Настройте узлы ПК.
Адреса ПК можно посмотреть в таблице адресации.

### Шаг 2. Выполните следующие тесты. Эхозапрос должен пройти успешно.
Примечание. Возможно, вам придется отключить брандмауэр ПК для работы ping
|	От	|	Протокол	|	Назначение	| Результат	|
|	---	|	---	|	---	| ---	|
|	PC-A	|	Ping	|	10.40.0.10	| ок	|
|	PC-A	|	Ping	|	10.20.0.1	| ок	|
|	PC-B	|	Ping	|	10.30.0.10	| ок	|
|	PC-B	|	Ping	|	10.20.0.1	| ок	|
|	PC-B	|	Ping	|	172.16.1.1	| ок	|
|	PC-B	|	HTTPS	|	10.20.0.1	| не получилось	|
|	PC-B	|	HTTPS	|	172.16.1.1	| не получилось	|
|	PC-B	|	SSH	|	10.20.0.1	| ок	|
|	PC-B	|	SSH	|	172.16.1.1	| ок	|

PC-A (ping)
```
C:\>ping 10.40.0.10

Pinging 10.40.0.10 with 32 bytes of data:

Request timed out.
Reply from 10.40.0.10: bytes=32 time<1ms TTL=127
Reply from 10.40.0.10: bytes=32 time=6ms TTL=127
Reply from 10.40.0.10: bytes=32 time<1ms TTL=127

Ping statistics for 10.40.0.10:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 6ms, Average = 2ms

C:\>ping 10.20.0.1

Pinging 10.20.0.1 with 32 bytes of data:

Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255

Ping statistics for 10.20.0.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
PC-B (ping)
```
C:\>ping 10.30.0.10

Pinging 10.30.0.10 with 32 bytes of data:

Reply from 10.30.0.10: bytes=32 time<1ms TTL=127
Reply from 10.30.0.10: bytes=32 time=1ms TTL=127
Reply from 10.30.0.10: bytes=32 time<1ms TTL=127
Reply from 10.30.0.10: bytes=32 time<1ms TTL=127

Ping statistics for 10.30.0.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms

C:\>ping 10.20.0.1

Pinging 10.20.0.1 with 32 bytes of data:

Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time=6ms TTL=255
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255

Ping statistics for 10.20.0.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 6ms, Average = 1ms

C:\>ping 172.16.1.1

Pinging 172.16.1.1 with 32 bytes of data:

Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
Reply from 172.16.1.1: bytes=32 time<1ms TTL=255

Ping statistics for 172.16.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
**PC-B (https) не получилась настройка.**<br>

PC-B (ssh)
```
C:\>ssh 10.20.0.1
Invalid Command.
C:\>ssh -l SSHadmin 10.20.0.1
Password: 
R1#exit
[Connection to 10.20.0.1 closed by foreign host]
C:\>
C:\>ssh -l SSHadmin 172.16.1.1
Password: 
R1#exit
[Connection to 172.16.1.1 closed by foreign host]
C:\>
```

## Часть 7. Настройка и проверка списков контроля доступа (ACL)
При проверке базового подключения компания требует реализации следующих политик безопасности:<br>
*Политика 1*. Сеть Sales не может использовать SSH в сети Management (но в  другие сети SSH разрешен).<br>
*Политика 2*. Сеть Sales не имеет доступа к IP-адресам в сети Management с помощью любого веб-протокола (HTTP/HTTPS). Сеть Sales также не имеет доступа к интерфейсам R1 с помощью любого веб-протокола. Разрешён весь другой веб-трафик (обратите внимание — Сеть Sales  может получить доступ к интерфейсу Loopback 1 на R1).<br>
*Политика 3*. Сеть Sales не может отправлять эхо-запросы ICMP в сети Operations или Management. Разрешены эхо-запросы ICMP к другим адресатам.<br>
*Политика 4*: Cеть Operations  не может отправлять ICMP эхозапросы в сеть Sales. Разрешены эхо-запросы ICMP к другим адресатам.<br>
### Шаг 1. Проанализируйте требования к сети и политике безопасности для планирования реализации ACL.

### Шаг 2. Разработка и применение расширенных списков доступа, которые будут соответствовать требованиям политики безопасности.
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip access-list extended SALES_ACL
R1(config-ext-nacl)#remark policy 1_no SSH to Managment
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 22
R1(config-ext-nacl)#remark policy 2_no http(https) to Managment/R1 interface
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq www
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 443
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.30.0.1 eq www
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.30.0.1 eq 443
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.40.0.1 eq www
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.40.0.1 eq 443
R1(config-ext-nacl)#remark policy 3_no ping to Operationa/Managment
R1(config-ext-nacl)#deny icmp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 echo
R1(config-ext-nacl)#deny icmp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 echo
R1(config-ext-nacl)#permit ip any any
R1(config-ext-nacl)#exit
R1(config)#ip access-list extended OPERATIONS_ACL
R1(config-ext-nacl)#remark policy 4_no ping to Sales
R1(config-ext-nacl)#deny icmp 10.30.0.0 0.0.0.255 10.40.0.0 0.0.0.255 echo
R1(config-ext-nacl)#permit ip any any
R1(config-ext-nacl)#exit
R1(config)#ibt f0/0/1.40
            ^
% Invalid input detected at '^' marker.
	
R1(config)#int f0/0/1.40
%Invalid interface type and number
R1(config)#int g0/0/1.40
R1(config-subif)#ip ac
R1(config-subif)#ip access-group SALES_ACL in
R1(config-subif)#int g0/0/1.30
R1(config-subif)#ip ac
R1(config-subif)#ip access-group OPERATIONS_ACL in
R1(config-subif)#
```
### Шаг 3. Убедитесь, что политики безопасности применяются развернутыми списками доступа.
Выполните следующие тесты. Ожидаемые результаты показаны в таблице:
От	Протокол	Назначение	Результат
PC-A	Ping.	10.40.0.10	Сбой
PC-A	Ping.	10.20.0.1	Успех
PC-B	Ping.	10.30.0.10	Сбой
PC-B	Ping.	10.20.0.1	Сбой
PC-B	Ping.	172.16.1.1	Успех
PC-B	HTTPS	10.20.0.1	Сбой
PC-B	HTTPS	172.16.1.1	Успех
PC-B	SSH	10.20.0.4	Сбой
PC-B	SSH	172.16.1.1	Успех
Конец документа
