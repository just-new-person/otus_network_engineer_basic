# Лабораторная работа - Реализация DHCPv4 

### Топология
![](Топология_21.png)

### Таблица адресации

|	Устройство	|	Интерфейс	|	IP-адрес	|	Маска подсети	|	Шлюз по умолчанию	|
|---------------|---------------|---------------|-------------------|-----------------------|
|	R1	|	G0/0/0	|	10.0.0.1	|	255.255.255.252	|	—	|
|	R1	|	G0/0/1	|	—	|	—	|		|
|	R1	|	G0/0/1.100	|	192.168.1.1	|	255.255.255.192	|		|
|	R1	|	G0/0/1.200	|		|		|		|
|	R1	|	G0/0/1.1000	|	—	|	—	|		|
|	R2	|	G0/0	|	10.0.0.2	|	255.255.255.252	|	—	|
|	R2	|	G0/0/1	|	192.168.1.1	|	255.255.255.240	|		|
|	S1	|	VLAN 200	|	192.168.1.2	|	255.255.255.224	|	192.168.1.1	|
|	S2	|	VLAN 1	|		|		|		|
|	PC-A	|	NIC	|	DHCP	|	DHCP	|	DHCP	|
|	PC-B	|	NIC	|	DHCP	|	DHCP	|	DHCP	|

### Таблица VLAN

|	VLAN	|	Имя	|	Назначенный интерфейс	|
|-----------|-------|---------------------------|
|	1	|Нет	|S2: F0/18		|
|	100	|Клиенты	|S1: F0/6 		|
|	200	|Управление	|S1: VLAN 200  		|
|	999	|Parking_Lot	|S1: F0/1-4, F0/7-24, G0/1-2		|
|	1000	|Собственная	|	—		|

###  3.	Задачи
## Часть 1. Создание сети и настройка основных параметров устройства
## Часть 2. Настройка и проверка двух серверов DHCPv4 на R1
## Часть 3. Настройка и проверка DHCP-ретрансляции на R2

	Общие сведения/сценарий
Протокол динамической конфигурации сетевого узла (DHCP) — сетевой протокол, позволяющий сетевым администраторам управлять и автоматизировать назначение IP-адресов. Без использования DHCP  для IPv4 администратору необходимо вручную назначать и настраивать IP-адреса, предпочтительные DNS-серверы и шлюзы по умолчанию. По мере увеличения сети и перемещении устройств из одной внутренней сети в другую это становится административной проблемой.<br>
В предложенном сценарии размеры компании увеличились, и сетевые администраторы больше не имеют возможности назначать IP-адреса для устройств вручную. Ваша задача заключается в настройке маршрутизатора R1 для назначения IPv4-адресов в двух разных подсетях.<br>
Примечание: Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS.

	Необходимые ресурсы
●	2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
●	2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
●	2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
●	Консольные кабели для настройки устройств Cisco IOS через консольные порты.
●	Кабели Ethernet, расположенные в соответствии с топологией

### Решение

## Часть 1.	Создание сети и настройка основных параметров устройства
В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.



### Шаг 1.	Создание схемы адресации
Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:
#### a.	Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).
Подсеть A:<br>
Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100<br>
Количество узлов в подсети считается по формуле 2^n-2. Значит чтобы в этой подсети было возможно 58 хостов n должно быть равно 6 (количество бит для хостов в подсети).<br>
2^6-2=64-2=62 хоста.<br>
Значит количество бит подсети будет равно 32-6=26 бит (префикс /26).<br>
/26 просчитывая по ряду |128|64|32|16|8|4|2|1| дает нам 128+64=192<br>
Маска подсети будет равно 255.255.255.192<br>

**Первый IP** подсети 192.168.1.0 + 1 = **192.168.1.1**
Диапазон адресов будет от 192.168.1.0 (сетевой адрес) до 192.168.1.63 (широковещательный адрес broadcast). Итого 64 шт.

#### b.	Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1). 
Подсеть B:<br>
Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200. Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию.<br>
Количество узлов в подсети считается по формуле 2^n-2. Значит чтобы в этой подсети было возможно 28 хостов n должно быть равно 5 (количество бит для хостов в подсети).<br>
2^5-2=32-2=30 хоста.<br>
Значит количество бит подсети будет равно 32-5=27 бит (префикс /27).<br>
/27 просчитывая по ряду |128|64|32|16|8|4|2|1| дает нам 128+64+32=224<br>
Маска подсети будет равно 255.255.255.224<br>

Следующую подсеть B можно начинась со следующего свободного адреса IP 192.168.1.64<br>
**Второй IP** подсети 192.168.1.64 + 2 = **192.168.1.66**<br>
**Шлюз по умолчанию**, как правило, первый хостовый адрес подсети, значит будет **192.168.1.65**<br>
Диапазон адресов будет от 192.168.1.64 (сетевой адрес) до 192.168.1.95 (широковещательный адрес broadcast). Итого 32 шт.

#### c.	Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).
Подсеть C:
Запишите первый IP-адрес в таблице адресации для R2 G0/0/1.
Количество узлов в подсети считается по формуле 2^n-2. Значит чтобы в этой подсети было возможно 12 хостов n должно быть равно 4 (количество бит для хостов в подсети).<br>
2^4-2=16-2=14 хоста.<br>
Значит количество бит подсети будет равно 32-4=28 бит (префикс /28).<br>
/28 просчитывая по ряду |128|64|32|16|8|4|2|1| дает нам 128+64+32+16=240<br>
Маска подсети будет равно 255.255.255.240<br>

Следующую подсеть B можно начинать со следующего свободного адреса IP 192.168.1.96<br>
**Первый IP** подсети 192.168.1.96 + 1 = **192.168.1.97**<br>
Диапазон адресов будет от 192.168.1.96 (сетевой адрес) до 192.168.1.111 (широковещательный адрес broadcast). Итого 16 шт.



### Шаг 2.	Создайте сеть согласно топологии.
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.
![](Топология_21_вып.png)



### Шаг 3.	Произведите базовую настройку маршрутизаторов.
Настройка R2 будет произведена по аналогии с R1

#### a.	Назначьте маршрутизатору имя устройства.
Откройте окно конфигурации
```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1
R1(config)#
```
#### b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
```
R1(config)#no ip domain-lookup
```
#### c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
```
R1(config)#enable secret class
```
#### d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
```
R1(config)#line console 0
R1(config-line)#pas
R1(config-line)#password cisco
R1(config-line)#login
```
#### e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
```
R1(config)#line vty 0 4
R1(config-line)#pas
R1(config-line)#password cisco
R1(config-line)#login
```
#### f.	Зашифруйте открытые пароли.
```
R1(config-line)#
R1(config-line)#exit
R1(config)#serv
R1(config)#service pas
R1(config)#service password-encryption 
R1(config)#
```
#### g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
```
R1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is stricly phohibited. #
```
#### h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#wr
Building configuration...
[OK]
R1#
```
#### i.	Установите часы на маршрутизаторе на сегодняшнее время и дату.
Примечание. Вопросительный знак (?) позволяет открыть справку с правильной последовательностью параметров, необходимых для выполнения этой команды.
```
R1#clock set 22:09:26 10 aug 2026
```


### Шаг 4.	Настройка маршрутизации между сетями VLAN на маршрутизаторе R1.
#### a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.
```
R1(config)#int g0/0/1
R1(config-if)#no sh

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
```
#### b.	Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.
```
R1(config)#
R1(config)#int g0/0/1.100
R1(config-subif)#
%LINK-3-UPDOWN: Interface GigabitEthernet0/0/1.100, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.100, changed state to up

R1(config-subif)#en
R1(config-subif)#encapsulation d
R1(config-subif)#encapsulation dot1Q 100
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#des
R1(config-subif)#description Default Gateway for VLAN 100
R1(config-subif)#exit
R1(config)#
R1(config)# int g0/0/1.200
R1(config-subif)#
%LINK-3-UPDOWN: Interface GigabitEthernet0/0/1.200, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.200, changed state to up

R1(config-subif)#en
R1(config-subif)#encapsulation d
R1(config-subif)#encapsulation dot1Q 200
R1(config-subif)#ip address 192.168.1.66 255.255.255.224
R1(config-subif)#des
R1(config-subif)#description Default Gateway for VLAN 200
R1(config-subif)#exit
R1(config)#
R1(config)#int g0/0/1.1000
R1(config-subif)#
%LINK-3-UPDOWN: Interface GigabitEthernet0/0/1.1000, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.1000, changed state to up

R1(config-subif)#description myself own subnet
R1(config-subif)#exit
```

#### c.	Убедитесь, что вспомогательные интерфейсы работают.
```
R1(config)#do sh run
Building configuration...

Current configuration : 1127 bytes
!
version 15.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname R1
!
!
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
!
!
!
!
!
ip cef
no ipv6 cef
!
!
!
!
!
!
!
!
!
!
!
!
no ip domain-lookup
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface GigabitEthernet0/0/0
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface GigabitEthernet0/0/1
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/0/1.100
 description Default Gateway for VLAN 100
 encapsulation dot1Q 100
 ip address 192.168.1.1 255.255.255.192
!
interface GigabitEthernet0/0/1.200
 description Default Gateway for VLAN 200
 encapsulation dot1Q 200
 ip address 192.168.1.66 255.255.255.224
!
interface GigabitEthernet0/0/1.1000
 description myself own subnet
 no ip address
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
!
ip flow-export version 9
!
!
!
banner motd ^C
Unauthorized access is stricly phohibited. ^C
!
!
!
!
line con 0
 password 7 0822455D0A16
 logging synchronous
 login
!
line aux 0
!
line vty 0 4
 password 7 0822455D0A16
 login
!
!
!
end
```



### Шаг 5.	Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов.
#### a.	Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.
```
R2(config)#int g0/0/1
R2(config-if)#ip address 192.168.1.97 255.255.255.240
R2(config-if)#no sh

R2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up

R2(config-if)#
```
#### b.	Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.
Маршрутизатор R1
```
R1(config)#int g0/0/0
R1(config-if)#ip address 10.0.0.1 255.255.255.252
R1(config-if)#no sh

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up

R1(config-if)#
```
Маршрутизатор R2
```
R2(config)#int g0/0/0
R2(config-if)#ip address
R2(config-if)#ip address 10.0.0.2 255.255.255.252
R2(config-if)#no sh
R2(config-if)#no shutdown 

R2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up

R2(config-if)#
```

#### c.	Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.
Маршрутизатор R1
```
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
```
Маршрутизатор R2
```
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

#### d.	Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.
```
R1#ping 192.168.1.97

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```
#### e.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#wr
Building configuration...
[OK]
```



### Шаг 6.	Настройте базовые параметры каждого коммутатора.
Настройка маршрутизатора S2 аналогична настройке S1.
#### a.	Присвойте коммутатору имя устройства.
Откройте окно конфигурации
```
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#
```
#### b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
```
S1(config)#no ip domain0lookup
                       ^
% Invalid input detected at '^' marker.
	
S1(config)#no ip domain-lookup
```
#### c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
```
S1(config)#en
S1(config)#ena
S1(config)#enable sec
S1(config)#enable secret class
```
#### d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
```
S1(config)#line console 0
S1(config-line)#pas
S1(config-line)#password cisco
S1(config-line)#login
```
#### e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
```
S1(config-line)#line vty 0 4
S1(config-line)#pas
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#
```
#### f.	Зашифруйте открытые пароли.
```
S1(config-line)#exit
S1(config)#en
S1(config)#service en
S1(config)#service p
S1(config)#service password-encryption
```
#### g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
```
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is stricly phohibited. #

S1(config)#
```
#### h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
S1(config)#
S1(config)#
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#wr
Building configuration...
[OK]
```
#### i.	Установите часы на маршрутизаторе на сегодняшнее время и дату.
Примечание. Вопросительный знак (?) позволяет открыть справку с правильной последовательностью параметров, необходимых для выполнения этой команды.
```
S1#cl
S1#clo
S1#clock set
S1#clock set 22:22:00 13 aug 2026
```
#### j.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации.
```
S1#wr
Building configuration...
[OK]
```



### Шаг 7.	Создайте сети VLAN на коммутаторе S1.
Примечание. S2 настроен только с базовыми настройками.
#### a.	Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.
```
S1(config-vlan)#vlan 100
S1(config-vlan)#name Clients
S1(config)#vlan 200
S1(config-vlan)#name Management
S1(config-vlan)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#vlan 1000
S1(config-vlan)#name myself_own_subnet
S1(config-vlan)#
```
#### b.	Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.
```
S1(config)#int vlan 200
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan200, changed state to up

S1(config-if)#ip address 192.168.1.66 255.255.255.224
S1(config-if)#exit
S1(config)#
S1(config)#ip def
S1(config)#ip default-gateway 10.0.0.1
```
#### c.	Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2.
```
S2(config)#int vlan 1
S2(config-if)#ip ad
S2(config-if)#ip address 192.168.1.98 255.255.255.240
S2(config-if)#
S2#
%SYS-5-CONFIG_I: Configured from console by console

S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#ip def
S2(config)#ip default-gateway 10.0.0.2
S2(config)#
```
#### d.	Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.
Примечание. Команда interface range полезна для выполнения этой задачи с минимальным количеством команд.
Закройте окно настройки.
```
S1(config)#int range f0/1-4,f0/7-24,g0/1-2
S1(config-if-range)#sw
S1(config-if-range)#switchport mode acc
S1(config-if-range)#switchport mode access 
S1(config-if-range)#sw
S1(config-if-range)#switchport acc
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#sh
S1(config-if-range)#shutdown 

%LINK-5-CHANGED: Interface FastEthernet0/1, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/3, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/7, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/8, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/9, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/10, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/11, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/12, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/13, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/14, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/15, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/16, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/17, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/18, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/19, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/20, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/21, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/22, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/23, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down
S1(config-if-range)#
```



### Шаг 8.	Назначьте сети VLAN соответствующим интерфейсам коммутатора.
#### a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
Откройте окно конфигурации
```
S1(config)#int f0/6
S1(config-if)#sw
S1(config-if)#switchport mode
S1(config-if)#switchport mode ac
S1(config-if)#switchport mode access 
S1(config-if)#sw
S1(config-if)#switchport ac
S1(config-if)#switchport access vlan 100
```
#### b.	Убедитесь, что VLAN назначены на правильные интерфейсы.
```
S1(config-if)#do sh vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5
100  Clients                          active    Fa0/6
200  Management                       active    
999  Parking_Lot                      active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
1000 myself_own_subnet                active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
S1(config-if)#
```
Вопрос:<br>
Почему интерфейс F0/5 указан в VLAN 1?<br>
Ответ<br>
Потому что изначально все интерфейсы находятся во vlan 1. Далее, согласно указаний методички, мы загнали все интерфейсы, кроме f0/5-6, в Parking_Lot и отключили командой административно. F0/6 назначен во vlan 100 (Clients). Вот и получается, что f0/5 остался один во vlan 1.



### Шаг 9.	Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.
#### a.	Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.
#### b.	В рамках конфигурации транка  установите для native  VLAN значение 1000.
#### c.	В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.
#### d.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
#### e.	Проверьте состояние транка.
Вопрос:
Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?
Закройте окно настройки.
## Часть 2.	Настройка и проверка двух серверов DHCPv4 на R1
В части 2 необходимо настроить и проверить сервер DHCPv4 на R1. Сервер DHCPv4 будет обслуживать две подсети, подсеть A и подсеть C.
### Шаг 1.	Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A
#### a.	Исключите первые пять используемых адресов из каждого пула адресов.
Откройте окно конфигурации
#### b.	Создайте пул DHCP (используйте уникальное имя для каждого пула).
#### c.	Укажите сеть, поддерживающую этот DHCP-сервер.
#### d.	В качестве имени домена укажите CCNA-lab.com.
#### e.	Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.
#### f.	Настройте время аренды на 2 дня 12 часов и 30 минут.
#### g.	Затем настройте второй пул DHCPv4, используя имя пула R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.
### Шаг 2.	Сохраните конфигурацию.
Сохраните текущую конфигурацию в файл загрузочной конфигурации.
Закройте окно настройки.
### Шаг 3.	Проверка конфигурации сервера DHCPv4
#### a.	Чтобы просмотреть сведения о пуле, выполните команду show ip dhcp pool .
#### b.	Выполните команду show ip dhcp bindings для проверки установленных назначений адресов DHCP.
#### c.	Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.
### Шаг 4.	Попытка получить IP-адрес от DHCP на PC-A
#### a.	Из командной строки компьютера PC-A выполните команду ipconfig /all.
#### b.	После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.
#### c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.
## Часть 3.	Настройка и проверка DHCP-ретрансляции на R2
В части 3 настраивается R2 для ретрансляции DHCP-запросов из локальной сети на интерфейсе G0/0/1 на DHCP-сервер (R1). 
### Шаг 1.	Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1
#### a.	Настройте команду ip helper-address на G0/0/1, указав IP-адрес G0/0/0 R1.
Откройте окно конфигурации
#### b.	Сохраните конфигурацию.
### Шаг 2.	Попытка получить IP-адрес от DHCP на PC-B
#### a.	Из командной строки компьютера PC-B выполните команду ipconfig /all.
#### b.	После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.
#### c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.
#### d.	Выполните show ip dhcp binding для R1 для проверки назначений адресов в DHCP.
#### e.	Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.

