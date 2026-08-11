# Лабораторная работа - Настройка NAT для IPv4

###  Топология
![](Топология_35.png)

###  Таблица адресации

|		Устройство		|		Интерфейс		|		IP-адрес		|		Маска подсети		|	
|  -  |  -  |  -  |  -  |
|		R1		|		G0/0/0		|		209.165.200.230		|		255.255.255.248		|	
|		R1		|		G0/0/1		|		192.168.1.1		|		255.255.255.0		|	
|		R2		|		G0/0/0		|		209.165.200.225		|		255.255.255.248		|	
|		R2		|		Lo1		|		209.165.200.1		|		255.255.255.224		|	
|		S1		|		VLAN 1		|		192.168.1.11		|		255.255.255.0		|	
|		S2		|		VLAN 1		|		192.168.1.12		|		255.255.255.0		|	
|		PC-A		|		NIC		|		192.168.1.2		|		255.255.255.0		|	
|		PC-B		|		NIC		|		192.168.1.3		|		255.255.255.0		|	

### Задание

## Часть 1. Создание сети и настройка основных параметров устройства<br>
## Часть 2. Настройка и проверка NAT для IPv4<br>
## Часть 3. Настройка и проверка PAT для IPv4<br>
## Часть 4. Настройка и проверка статического NAT для IPv4.<br>

### Решение

Сеть состоит из двух Switch (C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4), двух Router (ISR Software (X86_64_LINUX_IOSD-UNIVERSALK9-M), Version 15.5(3)S5), и двух компьютеров PC-А и PC-B.<br>
Устройства соединены кабелем Ethernet (Cooper Straight-Throught) PC-B [FastEthernet0] --> [FastEthernet0/18] Switch S2 [FastEthernet0/1] --> [FastEthernet0/1] Switch S1; PC-A [FastEthernet0/0] --> [FastEthernet0/6] Switch S1 [FastEthernet0/5] --> [GigabitEyhernet0/0/1] Router R1 [GigabitEyhernet0/0/0] --> [GigabitEyhernet0/0/0] Router R2 --> Lo1 (loopback).

## Часть 1. Создание сети и настройка основных параметров устройства
В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

### Шаг 1. Подключите кабели сети согласно приведенной топологии.
Подключите устройства в соответствии с топологией и подсоедините соответствующие кабели.
![](Топология_35_вып.png)

### Шаг 2. Произведите базовую настройку маршрутизаторов.
Откройте окно конфигурации<br>
### a.	Назначьте маршрутизатору имя устройства.
Маршрутизатор R1
```
Router>en
Router#
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#hostname R1
R1(config)#
```
Маршрутизатор R2
```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R2
R2(config)#
```
### b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
Маршрутизатор R1
```
R1(config)#no ip domain-lookup
```
Маршрутизатор R2
```
R2(config)#no ip domain-lookup 
```
### c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
```
R1(config)#enable secret class
R1(config)#
```
Маршрутизатор R2
```
R2(config)#enable secret class
```
### d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
Маршрутизатор R1
```
R1(config)#line console 0
R1(config-line)#logging synchronous 
R1(config-line)#pass
R1(config-line)#password cisco
R1(config-line)#login
```
Маршрутизатор R2
```
R2(config)#line con
R2(config)#line console 0
R2(config-line)#pass
R2(config-line)#password cisco
R2(config-line)#login
```
### e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
Маршрутизатор R1
```
R1(config-line)#exit
R1(config)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#login
```
Маршрутизатор R2
```
R2(config-line)#ex
% Ambiguous command: "ex"
R2(config-line)#exit
R2(config)#line vty 0 4
R2(config-line)#pass
R2(config-line)#password cisco
R2(config-line)#login
```
### f.	Зашифруйте открытые пароли.
Маршрутизатор R1
```
R1(config-line)#exit
R1(config)#serv
R1(config)#service pas
R1(config)#service password-encryption 
R1(config)#
```
Маршрутизатор R2
```
R2(config)#serv
R2(config)#service pass
R2(config)#service password-encryption 
```
### g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
Маршрутизатор R1
```
R1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is stricly phohibited. #

R1(config)#
```
Маршрутизатор R2
```
R2(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is stricly phohibited. #

R2(config)#
```
### h.	Настройте IP-адресации интерфейса, как указано в таблице выше.
Маршрутизатор R1
```
R1(config)#int g0/0/0
R1(config-if)#ip ad
R1(config-if)#ip address 209.165.200.230 255.255.255.248
R1(config-if)#bo sh
               ^
% Invalid input detected at '^' marker.
	
R1(config-if)#no sh

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up
R1(config-if)#int g0/0/1
R1(config-if)#ip add 192.168.1.1 255.255.255.0
R1(config-if)#no sh

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
```
Маршрутизатор R2
```
R2(config)#int g0/0/0
R2(config-if)#ip add 209.165.200.225 255.255.255.248
R2(config-if)#int ?
% Unrecognized command
R2(config-if)#exit
R2(config)#int
R2(config)#interface ?
  Dialer            Dialer interface
  Dot11Radio        Dot11 interface
  Ethernet          IEEE 802.3
  FastEthernet      FastEthernet IEEE 802.3
  GigabitEthernet   GigabitEthernet IEEE 802.3z
  Loopback          Loopback interface
  Port-channel      Ethernet Channel of interfaces
  Serial            Serial
  Tunnel            Tunnel interface
  Virtual-Template  Virtual Template interface
  Vlan              Catalyst Vlans
  range             interface range command
R2(config)#interface lo
R2(config)#interface loopback ?
  <0-2147483647>  Loopback interface number
R2(config)#interface loopback 1

R2(config-if)#
%LINK-3-UPDOWN: Interface Loopback1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up

R2(config-if)#ip add 209.165.200.1 255.255.255.224
R2(config)#int g 0/0/0
R2(config-if)#no sh

R2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up
```
### i.	Настройте маршрут по умолчанию. от R2 до  R1.
Маршрутизатор R1 (настройка R1 не указана в задании методички, но без этого не будет работать)
```
R1(config)#ip route 209.165.200.0 255.255.255.248 209.165.200.225
R1(config)#
```
Маршрутизатор R2
```
R2(config)#ip route 192.168.1.0 255.255.255.0 209.165.200.230
R2(config)#
```
### j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
Маршрутизатор R1
```
R1#wr
Building configuration...
[OK]
R1#
```
Маршрутизатор R2
```
R2#wr
Building configuration...
[OK]
R2#
```
Закройте окно настройки.


### Шаг 3. Настройте базовые параметры каждого коммутатора.
Откройте окно конфигурации
### a.	Присвойте коммутатору имя устройства.
Коммутатор S1
```
Switch>
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#
```
Коммутатор S2
```
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S2
S2(config)#
```
### b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
Коммутатор S1
```
S1(config)#
S1(config)#no ip domain0l
S1(config)#no ip domain-l
S1(config)#no ip domain-lookup 
S1(config)#
```
Коммутатор S2
```
S2(config)#no ip domain-lookup 
S2(config)#
```
### c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
Коммутатор S1
```
S1(config)#ena
S1(config)#enable pas
S1(config)#enable sec
S1(config)#enable secret class
S1(config)#
```
Коммутатор S2
```
S2(config)#enable secret class
S2(config)#
```
### d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
Коммутатор S1
```
S1(config)#line con
S1(config)#line console 0
S1(config-line)#pass
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#
```
Коммутатор S2
```
S2(config)#line console 0
S2(config-line)#pas
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#
```
### e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
Коммутатор S1
```
S1(config-line)#int vty 0 4
                ^
% Invalid input detected at '^' marker.
	
S1(config-line)#ex
% Ambiguous command: "ex"
S1(config-line)#end
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#line vty 0 4
S1(config-line)#pas
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#
```
Коммутатор S2
```
S2(config-line)#line vty 0 4
S2(config-line)#pas
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#
```
### f.	Зашифруйте открытые пароли.
Коммутатор S1
```
S1(config)#service password-encryption 
S1(config)#
```
Коммутатор S2
```
S2(config-line)#
S2#
%SYS-5-CONFIG_I: Configured from console by console

S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#service pas
S2(config)#service password-encryption 
S2(config)#
```
### g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
Коммутатор S1
```
S1(config)#motd ?
% Unrecognized command
S1(config)#ban
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is stricly phohibited. #

S1(config)#
```
Коммутатор S2
```
S2(config)#ban
S2(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is stricly phohibited. #

S2(config)#
```
### h.	Выключите все интерфейсы, которые не будут использоваться.
Коммутатор S1
```
S1(config)#int rang
S1(config)#int range fa0/2-3, fa0/7-24, g0/1-2
S1(config-if-range)#shutdown

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/3, changed state to administratively down

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

<details>
  <summary>проверка отключения интерфесов S1</summary>
S1(config-if-range)#do sh run<br>
Building configuration...<br>

Current configuration : 1478 bytes<br>
!<br>
version 15.0<br>
no service timestamps log datetime msec<br>
no service timestamps debug datetime msec<br>
service password-encryption<br>
!<br>
hostname S1<br>
!<br>
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1<br>
!<br>
!<br>
!<br>
no ip domain-lookup<br>
!<br>
!<br>
!<br>
spanning-tree mode pvst<br>
spanning-tree extend system-id<br>
!<br>
interface FastEthernet0/1<br>
!<br>
interface FastEthernet0/2<br>
 shutdown<br>
!<br>
interface FastEthernet0/3<br>
 shutdown<br>
!<br>
interface FastEthernet0/4<br>
!<br>
interface FastEthernet0/5<br>
!<br>
interface FastEthernet0/6<br>
!<br>
interface FastEthernet0/7<br>
 shutdown<br>
!<br>
interface FastEthernet0/8<br>
 shutdown<br>
!<br>
interface FastEthernet0/9<br>
 shutdown<br>
</details>

```
S1(config-if-range)#int ran
S1(config-if-range)#int ran fa0/2-4
S1(config-if-range)#sh 

%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to administratively down
```

<details>
	<summary>повторная проверка отключения интерфесов S1</summary>
S1(config-if-range)#do sh run<br>
Building configuration...<br>

Current configuration : 1488 bytes<br>
!<br>
version 15.0<br>
no service timestamps log datetime msec<br>
no service timestamps debug datetime msec<br>
service password-encryption<br>
!<br>
hostname S1<br>
!<br>
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1<br>
!<br>
!<br>
!<br>
no ip domain-lookup<br>
!<br>
!<br>
!<br>
spanning-tree mode pvst<br>
spanning-tree extend system-id<br>
!<br>
interface FastEthernet0/1<br>
!<br>
interface FastEthernet0/2<br>
 shutdown<br>
!<br>
interface FastEthernet0/3<br>
 shutdown<br>
!<br>
interface FastEthernet0/4<br>
 shutdown<br>
!<br>
interface FastEthernet0/5<br>
!<br>
interface FastEthernet0/6<br>
!<br>
interface FastEthernet0/7<br>
 shutdown<br>
!<br>
interface FastEthernet0/8<br>
 shutdown<br>
!<br>
interface FastEthernet0/9<br>
 shutdown<br>
!<br>
interface FastEthernet0/10<br>
 shutdown<br>
!<br>
interface FastEthernet0/11<br>
 shutdown<br>
!<br>
interface FastEthernet0/12<br>
 shutdown<br>
!<br>
interface FastEthernet0/13<br>
 shutdown<br>
!<br>
interface FastEthernet0/14<br>
 shutdown<br>
!<br>
interface FastEthernet0/15<br>
 shutdown<br>
!<br>
interface FastEthernet0/16<br>
 shutdown<br>
!<br>
interface FastEthernet0/17<br>
 shutdown<br>
!<br>
interface FastEthernet0/18<br>
 shutdown<br>
!<br>
interface FastEthernet0/19<br>
 shutdown<br>
!<br>
interface FastEthernet0/20<br>
 shutdown<br>
!<br>
interface FastEthernet0/21<br>
 shutdown<br>
!<br>
interface FastEthernet0/22<br>
 shutdown<br>
!<br>
interface FastEthernet0/23<br>
 shutdown<br>
!<br>
interface FastEthernet0/24<br>
 shutdown<br>
!<br>
interface GigabitEthernet0/1<br>
 shutdown<br>
!<br>
interface GigabitEthernet0/2<br>
 shutdown<br>
!<br>
interface Vlan1<br>
 no ip address<br>
 shutdown<br>
!<br>
banner motd ^C<br>
Unauthorized access is stricly phohibited. ^C<br>
!<br>
!<br>
!<br>
line con 0<br>
 password 7 0822455D0A16<br>
 login<br>
!<br>
line vty 0 4<br>
 password 7 0822455D0A16<br>
 login<br>
line vty 5 15<br>
 login<br>
!<br>
!<br>
!<br>
!<br>
end<br>
</details>

Коммутатор S2
```
S2(config)#int range fa0/2-17, fa0/19-24, g0/1-23
interface range not validated - command rejected
S2(config)#int range fa0/2-17, fa0/19-24, g0/1-2
S2(config-if-range)#sh

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/3, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/5, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/6, changed state to administratively down

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

%LINK-5-CHANGED: Interface FastEthernet0/19, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/20, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/21, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/22, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/23, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down
S2(config-if-range)#
```
<details>
<summary>проверка отключения интерфейсов S2</summary>
S2(config-if-range)#do sh run<br>
Building configuration...<br>

Current configuration : 1498 bytes<br>
!<br>
version 15.0<br>
no service timestamps log datetime msec<br>
no service timestamps debug datetime msec<br>
service password-encryption<br>
!<br>
hostname S2<br>
!<br>
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1<br>
!<br>
!<br>
!<br>
no ip domain-lookup<br>
!<br>
!<br>
!<br>
spanning-tree mode pvst<br>
spanning-tree extend system-id<br>
!<br>
interface FastEthernet0/1<br>
!<br>
interface FastEthernet0/2<br>
shutdown<br>
!<br>
interface FastEthernet0/3<br>
shutdown<br>
!<br>
interface FastEthernet0/4<br>
shutdown<br>
!<br>
interface FastEthernet0/5<br>
shutdown<br>
!<br>
interface FastEthernet0/6<br>
shutdown<br>
!<br>
interface FastEthernet0/7<br>
shutdown<br>
!<br>
interface FastEthernet0/8<br>
shutdown<br>
!<br>
interface FastEthernet0/9<br>
shutdown<br>
!<br>
interface FastEthernet0/10<br>
shutdown<br>
!<br>
interface FastEthernet0/11<br>
shutdown<br>
!<br>
interface FastEthernet0/12<br>
shutdown<br>
!<br>
interface FastEthernet0/13<br>
shutdown<br>
!<br>
interface FastEthernet0/14<br>
shutdown<br>
!<br>
interface FastEthernet0/15<br>
shutdown<br>
!<br>
interface FastEthernet0/16<br>
shutdown<br>
!<br>
interface FastEthernet0/17<br>
shutdown<br>
!<br>
interface FastEthernet0/18<br>
!<br>
interface FastEthernet0/19<br>
shutdown<br>
!<br>
interface FastEthernet0/20<br>
shutdown<br>
!<br>
interface FastEthernet0/21<br>
shutdown<br>
!<br>
interface FastEthernet0/22<br>
shutdown<br>
!<br>
interface FastEthernet0/23<br>
shutdown<br>
!<br>
interface FastEthernet0/24<br>
shutdown<br>
!<br>
interface GigabitEthernet0/1<br>
shutdown<br>
!<br>
interface GigabitEthernet0/2<br>
shutdown<br>
!<br>
interface Vlan1<br>
no ip address<br>
shutdown<br>
!<br>
banner motd ^C<br>
Unauthorized access is stricly phohibited. ^C<br>
!<br>
!<br>
!<br>
line con 0<br>
password 7 0822455D0A16<br>
login<br>
!<br>
line vty 0 4<br>
password 7 0822455D0A16<br>
login<br>
line vty 5 15<br>
login<br>
!<br>
!<br>
!<br>
!<br>
end<br>

S2(config-if-range)#<br>
</details>

### i.	Настройте IP-адресации интерфейса, как указано в таблице выше.
Коммутатор S1
```
S1(config)#int vlan 1
S1(config-if)#ip add 192.168.1.11 255.255.255.0
S1(config-if)#no sh

S1(config-if)#
%LINK-3-UPDOWN: Interface Vlan1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
```
Коммутатор S2
```
S2(config-if-range)#exit
S2(config)#int vlan 1
S2(config-if)#ip ad
S2(config-if)#ip address 192.168.1.12 255.255.255.0
S2(config-if)#no sh

S2(config-if)#
%LINK-3-UPDOWN: Interface Vlan1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
```
+ настройка шлюза (без настройки шлюза в Часть 2 - Шаг 2 - Пункт "c" пинг не пройдет)<br>
настройка S2 по аналогии
```
S1(config)#ip default-gateway 192.168.1.1
```
### j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
Коммутатор S1
```
S1(config-if)#wr
              ^
% Invalid input detected at '^' marker.
	
S1(config-if)#do wr
Building configuration...
[OK]
S1(config-if)#
```
Коммутатор S2
```
S2(config-if)#do wr
Building configuration...
[OK]
S2(config-if)#
```
Закройте окно настройки.

## Часть 2. Настройка и проверка NAT для IPv4.
В части 2 необходимо настроить и проверить NAT для IPv4.

### Шаг 1. Настройте NAT на R1, используя пул из трех адресов 209.165.200.226-209.165.200.228. 
Откройте окно конфигурации

### a.	Настройте простой список доступа, который определяет, какие хосты будут разрешены для трансляции. В этом случае все устройства в локальной сети R1 имеют право на трансляцию.
```
R1(config)#access-list 1 permit 192.168.1.0 0.0.0.255
```

### b.	Создайте пул NAT и укажите ему имя и диапазон используемых адресов.
```
R1(config)#ip nat pool PUBLIC_ACCESS 209.165.200.226 209.165.200.228 netmask 255.255.255.248
```
Примечание. Параметр маски сети не является разделителем IP-адресов. Это должна быть правильная маска подсети для назначенных адресов, даже если вы используете не все адреса подсети в пуле. 

### c.	Настройте перевод, связывая ACL и пул с процессом преобразования.
```
R1(config)#ip nat inside source list 1 pool PUBLIC_ACCESS
```
Примечание: Три очень важных момента. Во-первых, слово «inside» имеет решающее значение для работы такого рода NAT. Если вы опустить его, NAT не будет работать. Во-вторых, номер списка — это номер ACL, настроенный на предыдущем ### Шаге. В-третьих, имя пула чувствительно к регистру. 

### d.	Задайте внутренний (inside) интерфейс. 
```
R1(config)#int g0/0/1
R1(config-if)#ip nat inside
```

### e.	Определите внешний (outside) интерфейс.
```
R1(config-if)#int g0/0/0
R1(config-if)#ip nat outside
```

### Шаг 2. Проверьте и проверьте конфигурацию. 
### a.	С PC-B,  запустите эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните процесс поиска и устранения неполадок. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.

Эхо-запрос на 209.165.200.1 не проходил. Предполагаю, что это из-за того, что в Часть 1 - Шаг 2 -Пункт "i" указано только "Настройте маршрут по умолчанию. от R2 до  R1.". Но не указано на настройку маршрута по умолчанию от R1 к R2. Значит роутер не знает что сеть 209.165.200.0 /29 находится за 209.165.200.230. Прописав этот маршрут пинг пошел, а таблица адресации, после пинга с PC-B, получилась вот такой.<br>
```
R1#show ip nat translations
Pro  Inside global     Inside local       Outside local      Outside global
icmp 209.165.200.227:37192.168.1.3:37     209.165.200.1:37   209.165.200.1:37
icmp 209.165.200.227:38192.168.1.3:38     209.165.200.1:38   209.165.200.1:38
icmp 209.165.200.227:39192.168.1.3:39     209.165.200.1:39   209.165.200.1:39
icmp 209.165.200.227:40192.168.1.3:40     209.165.200.1:40   209.165.200.1:40
```
Вопросы:<br>
Во что был транслирован внутренний локальный адрес PC-B?<br>
Какой тип адреса NAT является переведенным адресом?<br>
Ответ:<br>
Внутренний локальный адрес PC-B был транслирован в **209.165.200.227** с разными портами (37192,38192,39192,40192).<br>
**Inside global**
 
### b.	С PC-A, запустите  эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните отладку. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.
Пинг проходит. Талблица на R1:
```
R1#show ip nat translations
Pro  Inside global     Inside local       Outside local      Outside global
icmp 209.165.200.227:41192.168.1.3:41     209.165.200.1:41   209.165.200.1:41
icmp 209.165.200.227:42192.168.1.3:42     209.165.200.1:42   209.165.200.1:42
icmp 209.165.200.227:43192.168.1.3:43     209.165.200.1:43   209.165.200.1:43
icmp 209.165.200.227:44192.168.1.3:44     209.165.200.1:44   209.165.200.1:44
icmp 209.165.200.228:5 192.168.1.2:5      209.165.200.1:5    209.165.200.1:5
icmp 209.165.200.228:6 192.168.1.2:6      209.165.200.1:6    209.165.200.1:6
icmp 209.165.200.228:7 192.168.1.2:7      209.165.200.1:7    209.165.200.1:7
icmp 209.165.200.228:8 192.168.1.2:8      209.165.200.1:8    209.165.200.1:8
```

### c.	Обратите внимание, что предыдущая трансляция для PC-B все еще находится в таблице. Из S1, эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните отладку. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.
```
R1#sh ip nat translations 
Pro  Inside global     Inside local       Outside local      Outside global
icmp 209.165.200.226:1 192.168.1.3:1      209.165.200.1:1    209.165.200.1:1
icmp 209.165.200.226:2 192.168.1.3:2      209.165.200.1:2    209.165.200.1:2
icmp 209.165.200.226:3 192.168.1.3:3      209.165.200.1:3    209.165.200.1:3
icmp 209.165.200.226:4 192.168.1.3:4      209.165.200.1:4    209.165.200.1:4
icmp 209.165.200.227:1 192.168.1.2:1      209.165.200.1:1    209.165.200.1:1
icmp 209.165.200.227:2 192.168.1.2:2      209.165.200.1:2    209.165.200.1:2
icmp 209.165.200.227:3 192.168.1.2:3      209.165.200.1:3    209.165.200.1:3
icmp 209.165.200.227:4 192.168.1.2:4      209.165.200.1:4    209.165.200.1:4
icmp 209.165.200.228:2 192.168.1.11:2     209.165.200.1:2    209.165.200.1:2
icmp 209.165.200.228:3 192.168.1.11:3     209.165.200.1:3    209.165.200.1:3
icmp 209.165.200.228:4 192.168.1.11:4     209.165.200.1:4    209.165.200.1:4
icmp 209.165.200.228:5 192.168.1.11:5     209.165.200.1:5    209.165.200.1:5
```

### d.	Теперь запускаем пинг R2 Lo1 из S2. На этот раз перевод завершается неудачей, и вы получаете эти сообщения (или аналогичные) на консоли R1:
В примере из методички ошибка "Sep 23 15:43:55.562: %IOSXE-6-PLATFORM: R0/0: cpp_cp: QFP:0.0 Thread:000 TS:00000001473688385900 %NAT-6-ADDR_ALLOC_FAILURE: Address allocation failed; pool 1 may be exhausted [2]"<br>
В моем случае после цепочки пингов от трех устройств (PC-B, PC-A, S1) коммутатор S2 выдает такое сообщение:
```
S2#ping 209.165.200.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 209.165.200.1, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```

### e.	Это ожидаемый результат, потому что выделено только 3 адреса, и мы попытались ping Lo1 с четырех устройств. Напомним, что NAT — это трансляция «один-в-один». Как много выделено трансляций? Введите команду show ip nat translations verbose , и вы увидите, что ответ будет 24 часа.
В примере из методички указано<br>
R1# show ip nat translations verbose<br>
Pro Inside global Inside local Outside local Outside global<br>
--- 209.165.200.226 192.168.1.3 --- ---<br>
  create: 09/23/19 15:35:27, use: 09/23/19 15:35:27, timeout: 23:56:42<br>
  Map-Id(In): 1<br>
<output omitted><br>
У меня команда show ip nat translations verbose не срабатывает. Ругается на синтаксис. При попытке посмотреть через "?" вариант ввода доп. опции verbose не выдает. 
```
R1#show ip nat translations verbose
                            ^
% Invalid input detected at '^' marker.
	
R1#show ip nat translations ?
  <cr>
```

### f.	Учитывая, что пул ограничен тремя адресами, NAT для пула адресов недостаточно для нашего приложения. Очистите преобразование NAT и статистику, и мы перейдем к PAT.
В примере из методички указано<br>
R1# clear ip nat translations *<br>
R1# clear ip nat statistics<br>
Закройте окно настройки.

У первой команды буквы S в окончании нет
```
R1#clear ip nat ?
  translation  Clear dynamic translation
R1#clear ip nat tra
R1#clear ip nat translation 
```
Вторая предлагаемая команда не бьется. Как видим вариант только один - "translation".

## Часть 3. Настройка и проверка PAT для IPv4.
В части 3 необходимо настроить замену NAT на PAT в пул адресов, а затем на PAT с помощью интерфейса.

### Шаг 1. Удалите команду преобразования на R1.
Откройте окно конфигурации
Компоненты конфигурации преобразования адресов в основном одинаковы; что-то (список доступа) для идентификации адресов, пригодных для перевода, дополнительно настроенный пул адресов для их преобразования и команды, необходимые для идентификации внутреннего и внешнего интерфейсов. Из части 1 наш список доступа (список доступа 1) по-прежнему корректен для сетевого сценария, поэтому нет необходимости воссоздавать его. Мы будем использовать один и тот же пул адресов, поэтому нет необходимости воссоздавать эту конфигурацию. Кроме того, внутренний и внешний интерфейсы не меняются. Чтобы начать работу в части 3, удалите команду, связывающую ACL и пул вместе.<br>
```
R1(config)#no ip nat inside source list 1 pool PUBLIC_ACCESS
```

### Шаг 2. Добавьте команду PAT на R1.
Теперь настройте преобразование PAT в пул адресов (помните, что ACL и Pool уже настроены, так что это единственная команда, которую нам нужно изменить с NAT на PAT).
```
R1(config)#ip nat inside source list 1 pool PUBLIC_ACCESS overload
```

### Шаг 3. Протестируйте и проверьте конфигурацию.
### a.	Давайте проверим, что PAT работает. С PC-B,  запустите эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните отладку. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.
```
R1#sh ip nat tra
R1#sh ip nat translations 
Pro  Inside global     Inside local       Outside local      Outside global
icmp 209.165.200.228:21192.168.1.3:21     209.165.200.1:21   209.165.200.1:21
icmp 209.165.200.228:22192.168.1.3:22     209.165.200.1:22   209.165.200.1:22
icmp 209.165.200.228:23192.168.1.3:23     209.165.200.1:23   209.165.200.1:23
icmp 209.165.200.228:24192.168.1.3:24     209.165.200.1:24   209.165.200.1:24
```
Вопросы:<br>
Во что был транслирован внутренний локальный адрес PC-B?<br>
Какой тип адреса NAT является переведенным адресом?<br>
Чем отличаются выходные данные команды show ip nat translations из упражнения NAT?<br>
Ответы:<br>
Внутренний локальный 192.168.1.3 был транслирован во внутренний глобальный **209.165.200.228**<br>
**Inside global**<br>
В упражнении NAT адреса перебирались из трех по очереди (209.165.200.226, 209.165.200.227, 209.165.200.228). Тут **адрес один** - 209.165.200.228. **но перебором идут порты** (:21,:22,:23,:24).
 
### b.	С PC-A, запустите эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните отладку. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.
```
R1#sh ip nat translations 
Pro  Inside global     Inside local       Outside local      Outside global
icmp 209.165.200.228:21192.168.1.2:21     209.165.200.1:21   209.165.200.1:21
icmp 209.165.200.228:22192.168.1.2:22     209.165.200.1:22   209.165.200.1:22
icmp 209.165.200.228:23192.168.1.2:23     209.165.200.1:23   209.165.200.1:23
icmp 209.165.200.228:24192.168.1.2:24     209.165.200.1:24   209.165.200.1:24
```
Обратите внимание, что есть только одна трансляция. Отправьте ping еще раз, и быстро вернитесь к маршрутизатору и введите команду show ip nat translations verbose , и вы увидите, что произошло.
```
R1#sh ip nat translations 
Pro  Inside global     Inside local       Outside local      Outside global
icmp 209.165.200.228:21192.168.1.2:21     209.165.200.1:21   209.165.200.1:21
icmp 209.165.200.228:22192.168.1.2:22     209.165.200.1:22   209.165.200.1:22
icmp 209.165.200.228:23192.168.1.2:23     209.165.200.1:23   209.165.200.1:23
icmp 209.165.200.228:24192.168.1.2:24     209.165.200.1:24   209.165.200.1:24
icmp 209.165.200.228:25192.168.1.2:25     209.165.200.1:25   209.165.200.1:25
icmp 209.165.200.228:26192.168.1.2:26     209.165.200.1:26   209.165.200.1:26
```
предложенная команда "R1# show ip nat translations verbose" у меня не срабатывает (см. ранее Часть 2 - Шаг 2 - Пункт "e")

### c.	Генерирует трафик с нескольких устройств для наблюдения PAT. На PC-A и PC-B используйте параметр -t с командой ping, чтобы отправить безостановочный ping на интерфейс Lo1 R2 (ping -t 209.165.200.1), затем вернитесь к R1 и выполните команду show ip nat translations:
```
R1#show ip nat translations
Pro  Inside global     Inside local       Outside local      Outside global
icmp 209.165.200.228:1024192.168.1.3:29     209.165.200.1:29   209.165.200.1:1024
icmp 209.165.200.228:1025192.168.1.3:30     209.165.200.1:30   209.165.200.1:1025
icmp 209.165.200.228:1026192.168.1.3:31     209.165.200.1:31   209.165.200.1:1026
icmp 209.165.200.228:25192.168.1.3:25     209.165.200.1:25   209.165.200.1:25
icmp 209.165.200.228:26192.168.1.3:26     209.165.200.1:26   209.165.200.1:26
icmp 209.165.200.228:27192.168.1.3:27     209.165.200.1:27   209.165.200.1:27
icmp 209.165.200.228:28192.168.1.3:28     209.165.200.1:28   209.165.200.1:28
icmp 209.165.200.228:29192.168.1.2:29     209.165.200.1:29   209.165.200.1:29
icmp 209.165.200.228:30192.168.1.2:30     209.165.200.1:30   209.165.200.1:30
icmp 209.165.200.228:31192.168.1.2:31     209.165.200.1:31   209.165.200.1:31
icmp 209.165.200.228:32192.168.1.2:32     209.165.200.1:32   209.165.200.1:32
icmp 209.165.200.228:33192.168.1.2:33     209.165.200.1:33   209.165.200.1:33
icmp 209.165.200.228:34192.168.1.2:34     209.165.200.1:34   209.165.200.1:34
icmp 209.165.200.228:35192.168.1.2:35     209.165.200.1:35   209.165.200.1:35
icmp 209.165.200.228:36192.168.1.2:36     209.165.200.1:36   209.165.200.1:36
icmp 209.165.200.228:37192.168.1.2:37     209.165.200.1:37   209.165.200.1:37
icmp 209.165.200.228:38192.168.1.2:38     209.165.200.1:38   209.165.200.1:38
icmp 209.165.200.228:39192.168.1.2:39     209.165.200.1:39   209.165.200.1:39
icmp 209.165.200.228:40192.168.1.2:40     209.165.200.1:40   209.165.200.1:40
icmp 209.165.200.228:41192.168.1.2:41     209.165.200.1:41   209.165.200.1:41
```
Обратите внимание, что внутренний глобальный адрес одинаков для обоих сеансов.<br>
Вопрос:<br>
Как маршрутизатор отслеживает, куда идут ответы?<br>
Ответ:<br>
**По номеру порта** с которого пришел запрос - там тоже один адрес PC-A (192.168.1.2) и PC-B (192.168.1.3) с изменяющимся номером порта.
 
### d.	PAT в пул является очень эффективным решением для малых и средних организаций. Тем не менее есть неиспользуемые адреса IPv4, задействованные в этом сценарии. Мы перейдем к PAT с перегрузкой интерфейса, чтобы устранить эту трату IPv4 адресов. Остановите ping на PC-A и PC-B с помощью комбинации клавиш Control-C, затем очистите трансляции и статистику:
```
R1#clear ip nat translation *
```
Вторая предлагаемая команда (R1# clear ip nat statistics) не бьется. Вариант опции только один - "translation" (см. ранее Часть 2 - Шаг 2 - Пункт "f").

### Шаг 4. На R1 удалите команды преобразования nat pool.
Опять же, наш список доступа (список доступа 1) по-прежнему корректен для сетевого сценария, поэтому нет необходимости воссоздавать его. Кроме того, внутренний и внешний интерфейсы не меняются. Чтобы начать работу с PAT к интерфейсу, очистите конфигурацию, удалив пул NAT и команду, связывающую ACL и пул вместе.
```
R1(config)#no ip nat inside source list 1 pool PUBLIC_ACCESS ocerload
R1(config)#no ip nat pool PUBLIC_ACCESS
```
### Шаг 5. Добавьте команду PAT overload, указав внешний интерфейс.
Добавьте команду PAT, которая вызовет перегрузку внешнего интерфейса.
```
R1(config)#ip nat inside source list 1 interface g0/0/0 overload
```

### Шаг 6. Протестируйте и проверьте конфигурацию. 
### a.	Давайте проверим PAT, чтобы интерфейс работал. С PC-B,  запустите эхо-запрос интерфейса Lo1 (209.165.200.1) на R2. Если эхо-запрос не прошел, выполните отладку. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations.
```
R1#show ip nat translations 
Pro  Inside global     Inside local       Outside local      Outside global
icmp 209.165.200.230:320192.168.1.3:320    209.165.200.1:320  209.165.200.1:320
icmp 209.165.200.230:321192.168.1.3:321    209.165.200.1:321  209.165.200.1:321
icmp 209.165.200.230:322192.168.1.3:322    209.165.200.1:322  209.165.200.1:322
icmp 209.165.200.230:323192.168.1.3:323    209.165.200.1:323  209.165.200.1:323
```
### b.	Сделайте трафик с нескольких устройств для наблюдения PAT. На PC-A и PC-B используйте параметр -t с командой ping для отправки безостановочного ping на интерфейс Lo1 R2 (ping -t 209.165.200.1). На S1 и S2 выполните привилегированную команду exec ping 209.165.200.1 повторить 2000. Затем вернитесь к R1 и выполните команду show ip nat translations.
На коммутаторах S1 и S2 команда предложенная команда "ping 209.165.200.1 repeat 2000" не сработала.
```
C:\>ping 209.165.200.1 repeat 2000
Invalid Command.
```
Похожая по написанию команда есть на PC
```
C:\>ping
Cisco Packet Tracer PC Ping
Usage: ping [-n count | -v TOS | -t ] target
```
На коммутаторах S1 и S2 пришлось задавать параметры вручную (помог интернет)
```
S2#ping
Protocol [ip]: 
Target IP address: 209.165.200.1
Repeat count [5]: 2000
Datagram size [100]: 
Timeout in seconds [2]: 
Extended commands [n]: 
Sweep range of sizes [n]: 
Type escape sequence to abort.
Sending 2000, 100-byte ICMP Echos to 209.165.200.1, timeout is 2 seconds:
```
Результат команды "R1#show ip nat translations" на R1:
```
R1#show ip nat translations 
Pro  Inside global     Inside local       Outside local      Outside global
icmp 209.165.200.230:100192.168.1.11:100   209.165.200.1:100  209.165.200.1:100
icmp 209.165.200.230:101192.168.1.11:101   209.165.200.1:101  209.165.200.1:101
icmp 209.165.200.230:1024192.168.1.12:57    209.165.200.1:57   209.165.200.1:1024
icmp 209.165.200.230:1025192.168.1.12:67    209.165.200.1:67   209.165.200.1:1025
icmp 209.165.200.230:1026192.168.1.12:77    209.165.200.1:77   209.165.200.1:1026
icmp 209.165.200.230:1027192.168.1.12:86    209.165.200.1:86   209.165.200.1:1027
icmp 209.165.200.230:1028192.168.1.12:95    209.165.200.1:95   209.165.200.1:1028
icmp 209.165.200.230:1029192.168.1.12:105   209.165.200.1:105  209.165.200.1:1029
icmp 209.165.200.230:102192.168.1.11:102   209.165.200.1:102  209.165.200.1:102
icmp 209.165.200.230:1030192.168.1.12:114   209.165.200.1:114  209.165.200.1:1030
icmp 209.165.200.230:1031192.168.1.12:124   209.165.200.1:124  209.165.200.1:1031
icmp 209.165.200.230:1032192.168.1.12:133   209.165.200.1:133  209.165.200.1:1032
icmp 209.165.200.230:1033192.168.1.3:582    209.165.200.1:582  209.165.200.1:1033
icmp 209.165.200.230:1034192.168.1.3:583    209.165.200.1:583  209.165.200.1:1034
icmp 209.165.200.230:1035192.168.1.3:584    209.165.200.1:584  209.165.200.1:1035
icmp 209.165.200.230:1036192.168.1.3:585    209.165.200.1:585  209.165.200.1:1036
icmp 209.165.200.230:1037192.168.1.3:586    209.165.200.1:586  209.165.200.1:1037
icmp 209.165.200.230:1038192.168.1.3:587    209.165.200.1:587  209.165.200.1:1038
icmp 209.165.200.230:1039192.168.1.3:588    209.165.200.1:588  209.165.200.1:1039
```
Теперь все внутренние глобальные адреса сопоставляются с IP-адресом интерфейса g0/0/0.<br>
Остановите все пинги. На PC-A и PC-B, используя комбинацию клавиш CTRL-C.<br>
Закройте окно настройки.

## Часть 4. Настройка и проверка статического NAT для IPv4.
В части 4 будет настроена статическая NAT таким образом, чтобы PC-A был доступен напрямую из Интернета. PC-A будет доступен из R2 по адресу 209.165.200.229.<br>
Примечание. Конфигурация, которую вы собираетесь завершить, не соответствует рекомендуемым практикам для шлюзов, подключенных к Интернету. Эта лаборатория полностью опускает стандартные методы безопасности, чтобы сосредоточиться на успешной конфигурации статического NAT. В производственной среде решающее значение для удовлетворения этого требования будет иметь тщательная координация между сетевой инфраструктурой и группами безопасности.<br>

### Шаг 1. На R1 очистите текущие трансляции и статистику.
Откройте окно конфигурации
```
R1#clear ip nat translation *
```
Вторая предлагаемая команда (R1# clear ip nat statistics) не бьется. Вариант опции только один - "translation" (см. ранее Часть 2 - Шаг 2 - Пункт "f"). ****

### Шаг 2. На R1 настройте команду NAT, необходимую для статического сопоставления внутреннего адреса с внешним адресом.
Для этого шага настройте статическое сопоставление между 192.168.1.11 и 209.165.200.1 с помощью следующей команды:
```
R1(config)#ip nat inside source static 192.168.1.11 209.165.200.1
```

### Шаг 3. Протестируйте и проверьте конфигурацию.
### a.	Давайте проверим, что статический NAT работает. На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations, и вы увидите статическое сопоставление.
```
R1#sh ip nat translations 
Pro  Inside global     Inside local       Outside local      Outside global
---  209.165.200.1     192.168.1.11       ---                ---
```

### b.	Таблица перевода показывает, что статическое преобразование действует. Проверьте это, запустив ping  с R2 на 209.165.200.229. Плинги должны работать.
Примечание. Возможно, вам придется отключить брандмауэр ПК для работы pings.<br>
В инструкции предлагают пустить пинг c R2 на 209.165.200.229, но в Шаге 2 статическое сопоставление мы сделали между 192.168.1.11 и 209.165.200.1. Значит пинг с надо отправлять не на 209.165.200.229, а на 209.168.200.1
```
R2#ping 209.165.200.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 209.165.200.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/1/3 ms
```

### c.	На R1 отобразите таблицу NAT на R1 с помощью команды show ip nat translations, и вы увидите статическое сопоставление и преобразование на уровне порта для входящих pings.
Это подтверждает, что статический NAT работает.
```
R1#sh ip nat translations 
Pro  Inside global     Inside local       Outside local      Outside global
---  209.165.200.1     192.168.1.11       ---                ---
```
