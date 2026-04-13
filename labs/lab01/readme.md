# Лабораторная работа. Базовая настройка коммутатора.
###  Топология
![](Топология.png)

###  Таблица адресации
| Устройство | Интерфейс | IP-адрес / префикс |
|------------|-----------|--------------------|
| S1 | VLAN 1 | 192.168.1.2 /24 |
| R1 | NIC | 192.168.1.10 /24 |

###  Задание:
<ol>
  <li>Проверка конфигурации коммутатора по умолчанию</li>
  <li>Создание сети и настройка основных параметров устройства
      <ol>
      2.1. Настройте базовые параметры коммутатора.<br>
      2.2. Настройте IP-адрес для ПК.
      </ol>
    </li>
  <li>Проверка сетевых подключений
  <ol>
    3.1. Отобразите конфигурацию устройства.<br>
    3.2. Протестируйте сквозное соединение, отправив эхо-запрос.<br>
    3.3. Протестируйте возможности удаленного управления с помощью Telnet.<br>
  </ol>
  </li>
</ol>

###  Решение:

## Часть 1. Создание сети и проверка настроек коммутатора по умолчанию

Шаг 1. Создание в Cisco Packet Tracer (далее CPT) сети, согласно топологии, состоящей из Switch (Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4) и PC.
Устройства соединены консольным кабелем (Console; Switch [Console] --> PC [RS-232]) и кабелем Ethernet (Cooper Straight-Throught; Switch [FastEthernet0/5] --> PC [FastEthernet0).
![](Топология_вып.png)

Шаг 2. Настройка коммутатора по умолчанию.
CPT --> PC --> вкладка Desctop --> Terminal

```
Switch>
```
#### a.
Вход в привелигированный режим EXEC
Ввод команды - Результат
```
Switch>enable
Switch#
```
Проверка файла конфигурации
Ввод команды - Результат
```
Switch#show running-config
```
<details>
  <summary>Результат команды show running-config</summary>
Building configuration...<br>
<br>
Current configuration : 1080 bytes<br>
!<br>
version 15.0<br>
no service timestamps log datetime msec<br>
no service timestamps debug datetime msec<br>
no service password-encryption<br>
!<br>
hostname Switch<br>
!<br>
!<br>
!<br>
!<br>
!<br>
!<br>
spanning-tree mode pvst<br>
spanning-tree extend system-id<br>
!<br>
interface FastEthernet0/1<br>
!<br>
interface FastEthernet0/2<br>
!<br>
interface FastEthernet0/3<br>
!<br>
interface FastEthernet0/4<br>
!<br>
interface FastEthernet0/5<br>
!<br>
interface FastEthernet0/6<br>
!<br>
interface FastEthernet0/7<br>
!<br>
interface FastEthernet0/8<br>
!<br>
interface FastEthernet0/9<br>
!<br>
interface FastEthernet0/10<br>
!<br>
interface FastEthernet0/11<br>
!<br>
interface FastEthernet0/12<br>
!<br>
interface FastEthernet0/13<br>
!<br>
interface FastEthernet0/14<br>
!<br>
interface FastEthernet0/15<br>
!<br>
interface FastEthernet0/16<br>
!<br>
interface FastEthernet0/17<br>
!<br>
interface FastEthernet0/18<br>
!<br>
interface FastEthernet0/19<br>
!<br>
interface FastEthernet0/20<br>
!<br>
interface FastEthernet0/21<br>
!<br>
interface FastEthernet0/22<br>
!<br>
interface FastEthernet0/23<br>
!<br>
interface FastEthernet0/24<br>
!<br>
interface GigabitEthernet0/1<br>
!<br>
interface GigabitEthernet0/2<br>
!<br>
interface Vlan1<br>
 no ip address<br>
 shutdown<br>
!<br>
!<br>
!<br>
!<br>
line con 0<br>
!<br>
line vty 0 4<br>
 login<br>
line vty 5 15<br>
 login<br>
!<br>
!<br>
!<br>
!<br>
end
</details>

#### b.
#### c.
#### d.
#### e.
#### f.
#### g.
#### h.
#### i.

## Часть 2. Настройка базовых параметров сетевых устройств

Шаг 1. Настройте базовые параметры коммутатора.

#### a
Настойка даты и времени
```
S1#clock ?
  set  Set the time and date
S1#clock set ?
  hh:mm:ss  Current Time
S1#clock set 21:12:00 ?
  <1-31>  Day of the month
  MONTH   Month of the year
S1#clock set 21:12:00 13 apr 2026
S1#
```
Переход в режим конфигурации сетевого устройства
```
Switch#
Switch#conf t
```
Отключение функцию поиска по DNS<br>
Изменение имени с Swith на S1<br>
Включение шифрование паролей (визуальное отображение)<br>
Назначание на вход в привелигированный режим (enable) пароль "class". Пароль в show running-config будет отображаться не в открытом виде<br>
Назначение банера motd ("сообщение дня") с разделителем #. Текст "Unauthorized access is strictly prohibited."<br>
```
Switch>
Switch>enable
Switch#
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#
Switch(config)#no ip domain ?
  lookup  Enable IP Domain Name System hostname translation
  name    Define the default domain name
Switch(config)#no ip domain loo
Switch(config)#no ip domain lookup 
Switch(config)#
Switch(config)#hostn S`
S`(config)#hostn S1
S1(config)#
S1(config)#service pas
S1(config)#service password-encryption 
S1(config)#
S1(config)#enab
S1(config)#enable secret class
S1(config)#
S1(config)#
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is stricly phohibited. #

S1(config)#
```
#### b.
Назначение IP-адрес интерфейсу SVI (switch virtual intarface) на коммутаторе. 'Благодаря этому получаем  возможность удаленного управления коммутатором.
```


Прежде чем вы сможете управлять коммутатором S1 удаленно с компьютера PC-A, коммутатору нужно назначить IP-адрес. Согласно конфигурации по умолчанию коммутатором можно управлять через VLAN 1.
c.	Доступ через порт консоли также следует ограничить  с помощью пароля. Используйте cisco в качестве пароля для входа в консоль в этом задании. Конфигурация по умолчанию разрешает все консольные подключения без пароля. Чтобы консольные сообщения не прерывали выполнение команд, используйте параметр logging synchronous.
S1(config)# line con 0
S1(config-line)# logging synchronous 
d.	Настройте каналы виртуального соединения для удаленного управления (vty), чтобы коммутатор разрешил доступ через Telnet. Если не настроить пароль VTY, будет невозможно подключиться к коммутатору по протоколу Telnet.
Вопрос:
Для чего нужна команда login?


Шаг 2. Настройте IP-адрес на компьютере PC-A.
Назначьте компьютеру IP-адрес и маску подсети в соответствии с таблицей адресации.



