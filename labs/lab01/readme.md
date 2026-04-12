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
<details>
  <summary>Нажми, чтобы раскрыть</summary>
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
</details>
```



