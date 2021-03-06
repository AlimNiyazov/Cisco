# **Vlan-Лабораторная работа №1**

## **Задание**

- Построение сети и настройка основных параметров устройства  
- Создание виртуальных локальных сетей и назначение портов коммутатора  
- Настройка магистрали 802.1Q между коммутаторами  
- Настройка маршрутизации между VLAN на маршрутизаторе  
- Проверка работы маршрутизации между VLAN  

## **1. Построение сети и настройка основных параметров на устройствах**

Подключаем устройства, как показано на схеме топологии. Topologiya


![alt text](Topologiya.png)



Настроим основные параметры для маршрутизатора:



- Настройка имени
- Установка пароля для привилегированного режима, консоли и VTY
- Отключение поиска DNS
- Создание баннера

### Конфиг :
```

enable secret Cisco
Router(config) # line console 0
Router(config-line) # login
Router(config-line) # password Cisco 

Router(config) # line aux 0
Router(config-line) # login
Router(config-line) # password Cisco 

Router0(config) # line vty 0 4
Router0(config-line) # login
Router0(config-line) # password Cisco 

```
### hostname R1


![alt text](R1.JPG)




Настроим основные параметры для коммутатора:


## **Настройка хостов ПК: Настраиваем по таблице**

` ` PC-A    NIC 192.168.3.3 255.255.255.0   192.168.3.1

` ` PC-B    NIC 192.168.4.3 255.255.255.0   192.168.4.1

## **2. Создание виртуальных локальных сетей и назначение портов коммутатора**

Создаем VLAN, как указано в таблице на обоих коммутаторах.  

Назначаем VLAN соответствующему интерфейсу.  

Устанавливаем шлюз по умолчанию  

Проверяем, что Vlan назначены правильным интерфейсам  

VLAN	Name	Interface Assigned  

` `	Management	S1: VLAN 3, S2: VLAN 3, S1: F0/6  
` `	Operations	S2: F0/18  
` `	ParkingLot	S1: F0/2-4, F0/7-24, G0/1-2, S2: F0/2-17, F0/19-24, G0/1-2  
` `	Native	N/A  

```
Конфиг S2: 

interface FastEthernet0/23
 switchport access vlan 7
 switchport mode access
 shutdown
!
interface FastEthernet0/24
 switchport access vlan 7
 switchport mode access
 shutdown
!
interface GigabitEthernet0/1
 switchport access vlan 7
 switchport mode access
 shutdown
!
interface GigabitEthernet0/2
 switchport access vlan 7
 switchport mode access
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
interface Vlan3
 ip address 192.168.3.12 255.255.255.0
!
interface Vlan4
 no ip address
!
interface Vlan7
 no ip address
!
banner motd "Attention! Unauthorized access is prohibited!"
!

```


![alt text](S2.JPG)

**Настройка магистрали 802.1Q между коммутаторами**

- Настраиваем интерфейс F0/1 в качестве магистрали на обоих коммутаторах

- Проверяем порты магистрали Конфиг S1:

```
!

interface FastEthernet0/1  

switchport trunk native vlan 8  

switchport trunk allowed vlan 3-4,8  

switchport mode trunk  

```

![alt text](S1.JPG)


` `Осуществляем проверку командой “show interfaces trunk”.

**Настройка маршрутизации между VLAN на маршрутизаторе**

` `Активируем интерфейс G0/0/1 на маршрутизаторе  
` `Настраиваем подинтерфейсы Vlan, как в таблице  
` `Проверяем работоспособность подинтерфейсов  
Таблица:  
```
Device	Interface	IP Address	Subnet Mask	Default Gateway
R1	G0/0/1.3	192.168.3.1	255.255.255.0	N/A
R1	G0/0/1.4	192.168.4.1	255.255.255.0	N/A
R1	G0/0/1.8	N/A	N/A	N/A
Конфиг R1:

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
interface GigabitEthernet0/0/1.3
 encapsulation dot1Q 3
 ip address 192.168.3.1 255.255.255.0
!
interface GigabitEthernet0/0/1.4
 encapsulation dot1Q 4
 ip address 192.168.4.1 255.255.255.0
!
interface GigabitEthernet0/0/1.8
 encapsulation dot1Q 8 native
 no ip address
!
interface Vlan1
 no ip address
 shutdown

```




` `Проверяем командой “show ip interface brief”
```
Interface              IP-Address      OK? Method Status                Protocol   
GigabitEthernet0/0/0   unassigned      YES NVRAM  administratively down down   
GigabitEthernet0/0/1   unassigned      YES NVRAM  up                    up   
GigabitEthernet0/0/1.3 192.168.3.1     YES manual up                    up   
GigabitEthernet0/0/1.4 192.168.4.1     YES manual up                    up   
GigabitEthernet0/0/1.8 unassigned      YES unset  up                    up   
Vlan1                  unassigned      YES unset  administratively down down  
```

**5. Проверка работы маршрутизации между VLAN**

### Проверка связи с PC-A на шлюз по умолчанию.  
- Пинг с PC-A на PC-B  
- Пинг с PC-A на S2  


![alt text](pingPC-A.JPG)  


![alt text](pingPC-B.JPG)  

