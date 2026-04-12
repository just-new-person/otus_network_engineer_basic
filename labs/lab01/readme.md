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



