# Настройка DHCPv4

Работа выполнена на ПК с установленным Cisco Packet Tracer.

#### Топология

[text](<../LR8_Настройка DHCPv6>)

#### Таблица адресации

 Устройство | Интерфейс | IP-адрес/
:----------:|:---------:|:----------------:
 R1         | G0/0/0    | 10.0.0.1\255.255.255.252
            | G0/0/1    | 
            | G0/0/1.100  | 
            | G0/0/1.200  |  
            | G0/0/1.1000 |            
 R2         | G0/0/0       |    10.0.0.1\255.255.255.252
            | G0/0/1    | 
    S1      | VLAN 200  |              
   S2       | VLAN 1    |           
  PC-A      | NIC       | DHCP             
   PC-B     | NIC       | DHCP           

#### Таблица VLAN

  VLAN | Имя   | Назначенный интерфейс
:----------:|:----------:|:----------------:
  1        | Нет         | S2: F0/18        
   100     | Клиенты     | S1: F0/6 
  200      | Управление  | S1: VLAN 200              
   999     | Parking_lot | S1: F0/1-4, F0/7-24, G0/1-2
  1000      | Собственная    | -             

### Часть 1. Создание сети и настройка основных параметров устройства

#### Шаг 1.1. Настройка базовых параметров каждого коммутатора

a.	Назначьте имя устройства.

```
Switch(config)#hos
Switch(config)#hostname S1
```

b.	Отключите поиск DNS, чтобы предотвратить попытки неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

```
S1(config)#NO IP DOMAIN-lookup 
```

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

```
S1(config)#enable s
S1(config)#enable secret class
```

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

```
S1(config)#line console 0
S1(config-line)#pas
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
```

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

```
S1(config)#line vty 0 4
S1(config-line)#pas
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
```

f.	Зашифруйте открытые пароли.

```
S1(config)#service password-encryption 
```

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

```
S1(config)#banner motd #AAAAA#
```

h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
S1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
```

Те же манипуляции проделаны со вторым коммутатором.

#### Шаг 1.2. Настройка базовых параметров каждого маршрутизатора

a.	Назначьте маршрутизатору имя устройства.

```
Router(config)#hostname R1
```

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

```
R1(config)#no ip domain-lookup 
```

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

```
R1(config)#enable secret class
```

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

```
R1(config)#line console 0
R1(config-line)#pas
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#ex
R1(config-line)#exi
R1(config-line)#exit 
```

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

```
R1(config)#line v
R1(config)#line vty 0 4
R1(config-line)#pas
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
```

f.	Зашифруйте открытые пароли.

```
R1(config)#service password-encryption 
```

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

```
R1(config)#banner motd #NO#
```

h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
R1#copy r
R1#copy running-config c
R1#copy running-config st
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```

Те же манипуляции проделаны со вторым маршрутизатором.

#### Шаг 1.3. Настройка маршрутизации между сетями VLAN на маршрутизаторе R1

a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int
R1(config)#interface g0/0/1
R1(config-if)#no sh
R1(config-if)#no shutdown 

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
exit
```

b.	Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

```
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int
R1(config)#interface g0/0/1
R1(config-if)#no sh
R1(config-if)#no shutdown 

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
exit
R1(config)#
R1(config)#interface g0/0/1.100
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.100, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.100, changed state to up

R1(config-subif)#des
R1(config-subif)#description VLAN_100_Clients
R1(config-subif)#encapsulation dot1q 100
R1(config-subif)#ip ad
R1(config-subif)#ip address 192.168.1.1. 255.255.255.192
                            ^
% Invalid input detected at '^' marker.
	
R1(config-subif)#ip address 192.168.1.1. 255.255.255.255
                            ^
% Invalid input detected at '^' marker.
	
R1(config-subif)#ip addres
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#exit
R1(config)#interface g0/0/1.200
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.200, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.200, changed state to up
description VLAN_100_management
R1(config-subif)#encapsulation dot1q 200
R1(config-subif)#ip address 192.168.1.65 255.255.255.224
R1(config-subif)#exit
R1(config)#interface g0/0/1.1000
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.1000, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.1000, changed state to up

R1(config-subif)#de
R1(config-subif)#des
R1(config-subif)#description VLAN_1000
R1(config-subif)#encapsulation dot1q 1000 n
R1(config-subif)#encapsulation dot1q 1000 native 
R1(config-subif)#no ip ad
R1(config-subif)#no ip address 
R1(config-subif)#exit
```

c.	Убедитесь, что вспомогательные интерфейсы работают.

```
R1#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
GigabitEthernet0/0/1   unassigned      YES unset  up                    up 
GigabitEthernet0/0/1.100192.168.1.1     YES manual up                    up 
GigabitEthernet0/0/1.200192.168.1.65    YES manual up                    up 
GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
R1#
```


#### Шаг 1.4. Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов

a.	Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.


```
R2(config)#interface g0/0/1
R2(config-if)#de
R2(config-if)#des
R2(config-if)#description S2_PC-B
R2(config-if)#IP ad
R2(config-if)#IP address 192.168.1.97 255.255.255.240
R2(config-if)#no sh
R2(config-if)#no shutdown 

R2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up

R2(config-if)#exit
```


b.	Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы 
IP-адресации.

```
R1(config)#interface g0/0/0
R1(config-if)#de
R1(config-if)#des
R1(config-if)#description to_R2
R1(config-if)#IP AD
R1(config-if)#IP ADdress 10.0.0.1 255.255.255.252
R1(config-if)#NO SH
R1(config-if)#NO SHutdown 

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up
```

```
R2(config)#interface g0/0/0
R2(config-if)#description to_R1
R2(config-if)#IP ADdress 10.0.0.2 255.255.255.252
R2(config-if)#no sh
R2(config-if)#no shutdown 
R2(config-if)#exit

R2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up
```

![alt text](image.png)

c.	Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 
на другом маршрутизаторе.

```
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

```
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
R2(config)#exit
```

d.	Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.

```
ping 192.168.1.97

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/0 ms

R1#ping 10.0.0.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```

```
R2#ping 10.0.0.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.0.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```

e.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
R1#copy r
R1#copy running-config st
Destination filename [startup-config]? 
Building configuration...
[OK]

R2#copy running-config st
R2#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
```

#### Шаг 1.5. Создайте сети VLAN на коммутаторе S1.

Примечание. S2 настроен только с базовыми настройками. 
a.	Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.

```
S1(config)#vlan 100
S1(config-vlan)#na
S1(config-vlan)#name clients
S1(config-vlan)#exit
S1(config)#vlan 200
S1(config-vlan)#name management
S1(config-vlan)#exit
S1(config)#vlan 1000
S1(config-vlan)#name native
S1(config-vlan)#exit
S1(config)#
```

b.	Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.

```
S1(config)#interface vl
S1(config)#interface vlan 200
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan200, changed state to up
des
S1(config-if)#description management_vlan_200
S1(config-if)#ip ad
S1(config-if)#ip address 192.168.1.66 255.255.255.224
S1(config-if)#no sh
S1(config-if)#no shutdown 
S1(config-if)#exit
```

```
S2(config)#ip de
S2(config)#ip default-gateway 192.168.1.65
```

c.	Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2

```
S2(config)#interface vl
S2(config)#interface vlan 1
S2(config-if)#des
S2(config-if)#description management_vlan_1
S2(config-if)#ip ad
S2(config-if)#ip address 192.168.1.98 255.255.255.240
S2(config-if)#no sh
S2(config-if)#no shutdown 
S1(config)#ip default-gateway 192.168.1.65
```

d.	Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.
Примечание. Команда interface range полезна для выполнения этой задачи с минимальным количеством команд.

```
S1(config)#interface range f0/1-4, f0/7-24, g0/1-2
S1(config-if-range)#swi
S1(config-if-range)#switchport m
S1(config-if-range)#switchport mode ac
S1(config-if-range)#switchport mode access 
S1(config-if-range)#sw
S1(config-if-range)#switchport ac
S1(config-if-range)#switchport access vl
S1(config-if-range)#switchport access vlan 999
% Access VLAN does not exist. Creating vlan 999
S1(config-if-range)#shutd
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
S1(config-if-range)#exit
```

```

S2(config)#vlan 999
S2(config-vlan)#name vlan999
S2(config-vlan)#exit
S2(config)#interface range f0/1-4, f0/6-17, f0/19-24, g0/1-2
S2(config-if-range)#sw
S2(config-if-range)#switchport m
S2(config-if-range)#switchport mode a
S2(config-if-range)#switchport mode access 
S2(config-if-range)#sw
S2(config-if-range)#switchport ac
S2(config-if-range)#switchport access vl
S2(config-if-range)#switchport access vlan 999
S2(config-if-range)#sh
S2(config-if-range)#shutdown 
S2(config-if-range)#exit

%LINK-5-CHANGED: Interface FastEthernet0/1, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/3, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to administratively down

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
```

#### Шаг 1.6. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

```
S1(config)#int
S1(config)#interface f0/6
S1(config-if)#des
S1(config-if)#description ac
S1(config-if)#description to_PC-A
S1(config-if)#sw
S1(config-if)#switchport m
S1(config-if)#switchport mode a
S1(config-if)#switchport mode access 
S1(config-if)#sw
S1(config-if)#switchport ac
S1(config-if)#switchport access vl
S1(config-if)#switchport access vlan 100
S1(config-if)#no sh
S1(config-if)#no shutdown 
S1(config-if)#exit
```

```
S2(config)#int f0/18
S2(config-if)#des
S2(config-if)#description to_PC-B
S2(config-if)#
S2(config-if)#sw
S2(config-if)#switchport m
S2(config-if)#switchport mode ac
S2(config-if)#switchport mode access 
S2(config-if)#sw
S2(config-if)#switchport ac
S2(config-if)#switchport access vl
S2(config-if)#switchport access vlan 1
S2(config-if)#no sh
S2(config-if)#no shutdown 
S2(config-if)#exit


```

b.	Убедитесь, что VLAN назначены на правильные интерфейсы.

```
S1#show vlan brief 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5
100  clients                          active    Fa0/6
200  management                       active    
999  VLAN0999                         active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
1000 native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

```
1    default                          active    Fa0/5, Fa0/18
999  vlan999                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/6, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/11, Fa0/12, Fa0/13
                                                Fa0/14, Fa0/15, Fa0/16, Fa0/17
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active   
```

- Почему интерфейс F0/5 указан в VLAN 1?
Потому что это транковый порт, а они отображаются в первом вилане.


#### Шаг 1.7. Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.

a.	Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.

```
S1(config)#int f0/5
S1(config-if)#sw
S1(config-if)#switchport m
S1(config-if)#switchport mode tr
S1(config-if)#switchport mode trunk 

S1(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/5, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/5, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan200, changed state to up
exit
```

b.	В рамках конфигурации транка  установите для native  VLAN значение 1000.

```
S1(config-if)#sw
S1(config-if)#switchport tr
S1(config-if)#switchport trunk n
S1(config-if)#switchport trunk native vl
S1(config-if)#switchport trunk native vlan 1000
```

c.	В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.

```
S1(config-if)#switchport trunk vla
S1(config-if)#switchport trunk vlan 100?
% Unrecognized command
S1(config-if)#switchport trunk vlan 100,200,1000
                               ^
% Invalid input detected at '^' marker.
	
S1(config-if)#switchport trunk allowed vlan 100,200,1000
```

d.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
S1#copy running-config st
S1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```

e.	Проверьте состояние транка.

```
S1#show int tr
Port        Mode         Encapsulation  Status        Native vlan
Fa0/5       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/5       100,200,1000

Port        Vlans allowed and active in management domain
Fa0/5       100,200,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/5       100,200,1000
```

- Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?
В диапазоне пула ip влана устройства, к которому пк подключен.  

### Часть 2. Настройка и проверка двух серверов DHCPv4 на R1

#### Шаг 2.1. Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A

a.	Исключите первые пять используемых адресов из каждого пула адресов.
Откройте окно конфигурации

```
R1(config)#ip dhcp po
R1(config)#ip dhcp ex
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.6
```

b.	Создайте пул DHCP (используйте уникальное имя для каждого пула).

```
R1(config)#ip dhcp pool R1_clients
```

c.	Укажите сеть, поддерживающую этот DHCP-сервер.

```
R1(dhcp-config)#network 192.168.1.0 255.255.255.192
```

d.	В качестве имени домена укажите CCNA-lab.com.

```
R1(dhcp-config)#domain-name CCNA-lab.com
```

e.	Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.

```
R1(dhcp-config)#default-router 192.168.1.1
```

f.	Настройте время аренды на 2 дня 12 часов и 30 минут.

```
R1(dhcp-config)#lease 2 12 30
                ^
% Invalid input detected at '^' marker.
	
R1(dhcp-config)#
R1(dhcp-config)#lea
R1(dhcp-config)#leas
R1(dhcp-config)#?
  default-router  Default routers
  dns-server      Set name server
  domain-name     Domain name
  exit            Exit from DHCP pool configuration mode
  network         Network number and mask
  no              Negate a command or set its defaults
  option          Raw DHCP options
R1(dhcp-config)#lease 2:12:30

R1(dhcp-config)#lease 3
                ^
% Invalid input detected at '^' marker
```

не дает настроить время аренды, будет по умолчанию - 1 день.

g.	Затем настройте второй пул DHCPv4, используя имя пула R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.

```
R1(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.102
R1(config)#ip d
R1(config)#ip dh
R1(config)#ip dhcp po
R1(config)#ip dhcp pool R2_
R1(config)#ip dhcp pool R2_clients
R1(dhcp-config)#ne
R1(dhcp-config)#network 192.168.1.96 255.255.255.240
R1(dhcp-config)#de
R1(dhcp-config)#default-router 192.168.1.97
R1(dhcp-config)#dom
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#lease 3
                ^
% Invalid input detected at '^' marker.
	
R1(dhcp-config)#exit
```

#### Шаг 2.2. Сохраните конфигурацию.

```
R1#copy running-config st
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```

#### Шаг 2.3. Проверка конфигурации сервера DHCPv4

a.	Чтобы просмотреть сведения о пуле, выполните команду show ip dhcp pool .

```
R1#show ip dhcp p
R1#show ip dhcp pool 

Pool R1_clients :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 62
 Leased addresses               : 1
 Excluded addresses             : 2
 Pending event                  : none

 1 subnet is currently in the pool
 Current index        IP address range                    Leased/Excluded/Total
 192.168.1.1          192.168.1.1      - 192.168.1.62      1    / 2     / 62

Pool R2_clients :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 14
 Leased addresses               : 0
 Excluded addresses             : 2
 Pending event                  : none

 1 subnet is currently in the pool
 Current index        IP address range                    Leased/Excluded/Total
 192.168.1.97         192.168.1.97     - 192.168.1.110     0    / 2     / 14
```

Нет адресса от PC-B (вижу что leased во втором пуле равен нулю), он не получает dhcp, разбираюсь и устраняю ошибки, которые мне не нравятся

```
S1(config)#vlan 999
S1(config-vlan)#name Parking_lot
S1(config-vlan)#exit
```

```
S2(config)#ip default-gateway 192.168.1.97
```

```
R1(config)#no ip dh
R1(config)#no ip dhcp po
R1(config)#no ip dhcp pool R2_clients
R1(config)#ip dh
R1(config)#ip dhcp po
R1(config)#ip dhcp pool R2_client_LAN
R1(dhcp-config)#new
R1(dhcp-config)#net
R1(dhcp-config)#network 192.168.1.96 255.255.255.240
R1(dhcp-config)#default-router 192.168.1.97
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#end
```

```
R2(config)#int g0/0/1
R2(config-if)#ip ad
R2(config-if)#ip he
R2(config-if)#ip hel
R2(config-if)#ip ?
  access-group     Specify access control for packets
  address          Set the IP address of an interface
  authentication   authentication subcommands
  flow             NetFlow Related commands
  hello-interval   Configures IP-EIGRP hello interval
  helper-address   Specify a destination address for UDP broadcasts
  inspect          Apply inspect name
  ips              Create IPS rule
  mtu              Set IP Maximum Transmission Unit
  nat              NAT interface commands
  ospf             OSPF interface commands
  proxy-arp        Enable proxy ARP
  split-horizon    Perform split horizon
  summary-address  Perform address summarization
R2(config-if)#ip helper-address 10.0.0.1
```

Проверяю теперь 

```
R1#show ip dhcp pool 

Pool R1_clients :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 62
 Leased addresses               : 1
 Excluded addresses             : 2
 Pending event                  : none

 1 subnet is currently in the pool
 Current index        IP address range                    Leased/Excluded/Total
 192.168.1.1          192.168.1.1      - 192.168.1.62      1    / 2     / 62

Pool R2_client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 14
 Leased addresses               : 1
 Excluded addresses             : 2
 Pending event                  : none

 1 subnet is currently in the pool
 Current index        IP address range                    Leased/Excluded/Total
 192.168.1.97         192.168.1.97     - 192.168.1.110     1    / 2     / 14
```

![alt text](image-1.png)

Теперь работает

b.	Выполните команду show ip dhcp binding для проверки установленных назначений адресов DHCP.

```
R1#
R1#show ip dhcp bi
R1#show ip dhcp binding 
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.1.7      0090.2B24.5AB0           --                     Automatic
192.168.1.103    0003.E451.CABD           --                     Automatic
```

c.	Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.

```
R1#show ip dhcp server statistics
                ^
% Invalid input detected at '^' marker.
R1#show ip dhcp ?
  binding   DHCP address bindings
  conflict  DHCP address conflicts
  pool      DHCP pools information
  relay     Miscellaneous DHCP relay information
R1#show ip dhcp 
R1#show ip dhcp server statistics
                ^
% Invalid input detected at '^' marker.	
```

#### Шаг 2.4. Попытка получить IP-адрес от DHCP на PC-A

a.	Из командной строки компьютера PC-A выполните команду ipconfig /all.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Physical Address................: 0090.2B24.5AB0
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.7
   Subnet Mask.....................: 255.255.255.192
   Default Gateway.................: ::
                                     192.168.1.1
   DHCP Servers....................: 192.168.1.1
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-69-46-E6-3E-00-90-2B-24-5A-B0
   DNS Servers.....................: ::
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: CCNA-lab.com
   Physical Address................: 0001.4388.0CB7
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-69-46-E6-3E-00-90-2B-24-5A-B0
   DNS Servers.....................: ::
                                     0.0.0.0
```

b.	После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.7
   Subnet Mask.....................: 255.255.255.192
   Default Gateway.................: ::
                                     192.168.1.1

Bluetooth Connection:

   Connection-specific DNS Suffix..: CCNA-lab.com
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
```

c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.

```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

### Часть 3. Настройка и проверка DHCP-ретрансляции на R2

#### Шаг 3.1. Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1

Было сделано выше при поиске ошибок

#### Шаг 3.2. Попытка получить IP-адрес от DHCP на PC-B

a.	Из командной строки компьютера PC-B выполните команду ipconfig /all.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Physical Address................: 0003.E451.CABD
   Link-local IPv6 Address.........: FE80::203:E4FF:FE51:CABD
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.103
   Subnet Mask.....................: 255.255.255.240
   Default Gateway.................: ::
                                     192.168.1.97
   DHCP Servers....................: 10.0.0.1
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-1B-14-2A-53-00-03-E4-51-CA-BD
   DNS Servers.....................: ::
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: CCNA-lab.com
   Physical Address................: 0007.EC46.3822
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-1B-14-2A-53-00-03-E4-51-CA-BD
   DNS Servers.....................: ::
                                     0.0.0.0
```

b.	После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Link-local IPv6 Address.........: FE80::203:E4FF:FE51:CABD
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.103
   Subnet Mask.....................: 255.255.255.240
   Default Gateway.................: ::
                                     192.168.1.97

Bluetooth Connection:

   Connection-specific DNS Suffix..: CCNA-lab.com
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
```

c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.

```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
Reply from 192.168.1.1: bytes=32 time<1ms TTL=254

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

```
C:\>ping 192.168.1.7

Pinging 192.168.1.7 with 32 bytes of data:

Reply from 192.168.1.7: bytes=32 time<1ms TTL=126
Reply from 192.168.1.7: bytes=32 time<1ms TTL=126
Reply from 192.168.1.7: bytes=32 time<1ms TTL=126
Reply from 192.168.1.7: bytes=32 time=1ms TTL=126

Ping statistics for 192.168.1.7:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms
```

d.	Выполните show ip dhcp binding для R1 для проверки назначений адресов в DHCP.

```
R1#show ip dhcp bi
R1#show ip dhcp binding 
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.1.7      0090.2B24.5AB0           --                     Automatic
192.168.1.103    0003.E451.CABD           --                     Automatic
R1#
R1#
R1#sh
R1#show ip dh
R1#show ip dhcp bi
R1#show ip dhcp binding 
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.1.7      0090.2B24.5AB0           --                     Automatic
192.168.1.103    0003.E451.CABD           --                     Automatic
```
