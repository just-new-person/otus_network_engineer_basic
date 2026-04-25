
# Лабораторная работа. Просмотр таблицы MAC-адресов коммутатора.
###  Топология
![](Топология_2.png)

###  Таблица адресации
| Устройство | Интерфейс | IP-адрес | Маска подсети | 
| - | - | - | - |
| S1 | VLAN 1 | 192.168.1.11 | 255.255.255.0 |
| S2 | VLAN 1 | 192.168.1.12 | 255.255.255.0 |
| PC-A | NIC | 192.168.1.1 | 255.255.255.0 |
| PC-B | NIC | 192.168.1.2 | 255.255.255.0 |

### Задание

Часть 1. Создание и настройка сети<br>
Часть 2. Изучение таблицы МАС-адресов коммутатора

### Решение

## Часть 1. Создание и настройка сети

### Шаг 1. Создание в CPT сети, согласно топологии

Сеть состоит из двух Switch (Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4) и двух PC.<br>
Устройства соединены кабелем Ethernet (Cooper Straight-Throught)<br>
  PC-A [FastEthernet0) --> S1 [FastEthernet0/6]<br>
  S1 [FastEthernet0/1] --> S2 [FastEthernet0/1]<br>
  S2 [FastEthernet0/18] --> PC-B [FastEthernet0)<br>
  
![](Топология_2_вып.png)

### Шаг 2. Настройка узлов ПК
В соответствии с таблицей адресации:<br>
![](Настройки_PC-A.png)
![](Настройки_PC-B.png)

### Шаг 3. Выполнение инициализации и перезагрузки коммутаторов.
Поскольку работа происходит в виртуальной среде CPT, то шаг 3 не требует исполнения - настройки чистые.<br>
Если бы было реальное сетевое оборудование, то в терминале выполняем команды (аналогично на обоих коммутаторах S1 и S2)
```
S1>enable
S1#erase startup-config 
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]y[OK]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
S1#reload
Proceed with reload? [confirm]yC2960
```

### Шаг 4. Настройте базовые параметры каждого коммутатора.
Создание соединения консольным кабелем (Console; S1 [Console] --> PC-A [RS-232])<br>
CPT --> PC-A --> вкладка Desktop --> Terminal

