# Лабораторная работа - Настройка NAT для IPv4

## Топология

![alt text](topology.png)

## Таблица адресации

![alt text](address.png)

## Часть 1. Создание сети и настройка основных параметров устройства

### Шаг 2. Произведите базовую настройку маршрутизаторов.

```
R1>enable
R1#conf t
Enter configuration commands, one per line. End with CNTL/Z.
R1 (config)#interface g0/0/0
R1(config-if)#ip address 209.165.200.230 255.255.255.248 R1(config-if)#no shutdown
R1 (config-if)#
*LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up
R1(config-if)#exit
R1 (config)#interface g0/0/1
R1(config-if)#ip address 192.168.1.1 255.255.255.0
R1 (config-if)#no shutdown
R1(config-if)#
LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up
& LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
R1(config-if)#exit
R1 (config) #exnd
Invalid input detected at
R1 (config) #end
R1#
marker.
SYS-5-CONFIG_I: Configured from console by console
R1#copy running-config startup-config Destination filename [startup-config)?
Building configuration...
[OK]
R1#
```

```
R2>enable
R2#conf t
Enter
configuration commands, one per line. End with CNTL/Z.
R2 (config)#interface g0/0/0
R2 (config-if)#ip address 209.165.200.225 255.255.255.248
R2 (config-if)#no shutdown
R2 (config-if)#
LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up
* LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up
R2 (config-if)#exit
R2 (config)#interface Lol
R2 (config-if)#
LINK-5-CHANGED: Interface Loopbackl, changed state to up
* LINEPROTO-5-UPDOWN: Line protocol on Interface Loopbackl, changed state to up
R2 (config-if)#ip address 209.165.200.1 255.255.255.224
R2 (config-if)#exit
R2 (config)#ip route 0.0.0.0 0.0.0.0 209.165.200.230 R2 (config)#end
R2#
\SYS-5-CONFIG_I: Configured from console by console
R2#copy running-config startup-config Destination filename [startup-config)? Building configuration...
[OK]
R2#
```

### Шаг 3. Настройте базовые параметры каждого коммутатора.

```
Sl>enable
Sl#conf t
Enter configuration commands, one per line. End with CNTL/Z. S1 (config)#interface vlan 1
S1 (config-if)#ip address 192.168.1.11 255.255.255.0
S1(config-if)#no shut
S1(config-if)#
LINK-5-CHANGED: Interface Vlanl, changed state to up
* LINEPROTO-5-UPDOWN: Line protocol on Interface Vlanl, changed state to up
S1(config-if)#exit
S1 (config)#interface range fa0/2-4, fa0/7-24, gi0/1-2
S1(config-if-range) # shutdown
```

```
S2>enable
S2#conf t
Enter configuration commands, one per line. End with CNTL/2. S2 (config)#interface vlan 1
S2 (config-if)# ip address 192.168.1.12 255.255.255.0
S2 (config-if)#no shut
S2 (config-if)#
LINK-5-CHANGED: Interface Vlanl, changed state to up
* LINEPROTO-5-UPDOWN: Line protocol on Interface Vlanl, changed state to up
S2 (config-if)#exit
S2 (config)#interface range fa0/2-17, fa0/19-24, gi0/1-2
S2 (config-if-range) #shutdown
```

## Часть 2. Настройка и проверка NAT для IPv4.

### Шаг 1. Настройте NAT на R1, используя пул из трех адресов 209.165.200.226-209.165.200.228. 

```
R1>enable
R1#conf t
Enter configuration commands, one per line. End with CNTL/Z.
R1 (config)#access-list 1 permit 192.168.1.0 0.0.0.255
R1(config)#ip nat pool PUBLIC ACCESS 209.165.200.226 209.165.200.228 netmask 255.255.255.248
R1 (config)#ip nat inside source list 1 pool PUBLIC ACCESS
R1 (config)#interface g0/0/1 R1(config-if)#ip nat inside. R1 (config-if)#exit
R1 (config)#interface g0/0/0 R1 (config-if)#ip nat outside R1(config-if)#exit
R1 (config)#copy running-config startup-config
Invalid input detected at
marker.
R1 (config)#exit
R1#
SYS-5-CONFIG_I: Configured from console by console
R1#copy running-config startup-config Destination filename [startup-config]? Building configuration....
[OK]
R1#
```

### Шаг 2. Проверьте и проверьте конфигурацию. 

Пререквизит к выполнению - добавить маршрут по умолчанию на R1 до R2, иначе ping с PC-B не пройдет - ip route 0.0.0.0 0.0.0.0 209.165.200.225

```
C:\>ping 209.165.200.1

Pinging 209.165.200.1 with 32 bytes of data:

Reply from 209.165.200.1: bytes=32 time<lms TTL=254
Reply from 209.165.200.1: bytes=32 time<lms TTL-254
Reply from 209.165.200.1: bytes=32 time<lms TTL=254
Reply from 209.165.200.1: bytes=32 time<lms TTL=254

Ping statistics for 209.165.200.1:
     Packets: Sent = 4, Received 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
     Minimum Oms, Maximum Oms, Average = Oms
```


Inside global
R1#show ip nat translations Pro icmp 209.165.200.226:25192.168.1.3:25 icmp 209.165.200.226:26192.168.1.3:26 icmp 209.165.200.226:27192.168.1.3:27 icmp 209.165.200.226:28192.168.1.3:28
Inside local
Outside local
Outside global
209.165.200.1:25
209.165.200.1:25
209.165.200.1:26
209.165.200.1:26
209.165.200.1:27
209.165.200.1:27
209.165.200.1:28
209.165.200.1:28

**Во что был транслирован внутренний локальный адрес PC-B?**

Внутренний локальный адрес PC-B 192.168.1.3 был преобразован в 209.165.200.226

**Какой тип адреса NAT является переведенным адресом?**

209.165.200.226 — Inside Global

![alt text](p2/s2/3.png)

![alt text](p2/s2/4.png)

Пререквизит для S1 и S2 - указать маршрут по умолчанию ip default-gateway 192.168.1.1

![alt text](p2/s2/5.png)

![alt text](p2/s2/6.png)

![alt text](p2/s2/7.png)

Команды show ip nat translations verbose нет в CPT

## Часть 3. Настройка и проверка PAT для IPv4.

### Шаг 1-3. Удалите команду преобразования на R1. Добавьте команду PAT на R1.Протестируйте и проверьте конфигурацию.

![alt text](p3/s1/1.png)

**Во что был транслирован внутренний локальный адрес PC-B?**

Inside global 209.165.200.226

**Какой тип адреса NAT является переведенным адресом?**

Inside global address

**Чем отличаются выходные данные команды show ip nat translations из упражнения NAT?**

Обычный NAT выделяет уникальный глобальный адрес из пула

![alt text](p3/s1/2.png)

![alt text](p3/s1/3.png)

Команды show ip nat translations verbose нет в CPT

**Как маршрутизатор отслеживает, куда идут ответы?** 

Маршрутизатор использует уникальные номера портов в таблице PAT чтобы определить, какому устройству нужно передать полученный ответ.

### Шаг 4-6. На R1 удалите команды преобразования nat pool. Добавьте команду PAT overload, указав внешний интерфейс. Протестируйте и проверьте конфигурацию. 

![alt text](p3/s4/1.png)

## Часть 4. Настройка и проверка статического NAT для IPv4.

### Шаг 2-3. На R1 настройте команду NAT, необходимую для статического сопоставления внутреннего адреса с внешним адресом. Протестируйте и проверьте конфигурацию.

![alt text](p4/s2/1.png)

