# Лабораторная работа. Базовая настройка коммутатора 

## Топология 
![](2026-07-14_16-27-01.png)

## Таблица адресации 
![](2026-07-14_16-29-20.png)

 ##  Задачи:

 ## Часть 1. Создание и настройка сети

### Настройте базовые параметры каждого коммутатора

b.	Настройте IP-адреса, как указано в таблице адресации.

![](2026-07-14_16-40-03.png)

![](2026-07-14_16-45-25.png)

![](2026-07-14_17-12-34.png) 

![](2026-07-14_17-15-24.png)

c.	Назначьте cisco в качестве паролей консоли и VTY.

![](2026-07-14_17-19-36.png)

![](2026-07-14_17-21-36.png)

d.	Назначьте class в качестве пароля доступа к привилегированному режиму EXEC.

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#service password-encryption
S1(config)#enable secret class
```
```
S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#service password-encryption
S2(config)#enable secret class
```
## Часть 2. Изучение таблицы МАС-адресов коммутатора

### Запишите МАС-адреса сетевых устройств.

MAC-адрес компьютера PC-A: 0060.5c2c.70d9

MAC-адрес компьютера PC-B: 0090.0C4B.4E1E

МАС-адрес коммутатора S1 Fast Ethernet 0/1: 00e0.b035.e635

МАС-адрес коммутатора S2 Fast Ethernet 0/1: 0001.6499.5073

### Просмотрите таблицу МАС-адресов коммутатора.

```
S2#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    0009.7c0b.1d01    DYNAMIC     Fa0/1
   1    0060.5c2c.70d9    DYNAMIC     Fa0/1
   1    0090.0c4b.4e1e    DYNAMIC     Fa0/2
   1    00e0.b035.e635    DYNAMIC     Fa0/1
```

### Очистите таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов.

Таблица MAC адресов коммутатора S2 сразу после выполнения команды clear mac address-table dynamic не содержат никаких записей

```
S2#clear mac address-table dynamic
S2#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
```

Через 10 секунд в таблице появилась запись о MAC адресе коммутатора S1

```
S2#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    0009.7c0b.1d01    DYNAMIC     Fa0/1
```
### С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.

Изначально таблица записей ARP-кэша пуста

```
C:\>arp -a
No ARP Entries Found
```

Выполянем ping всех устройств сети

```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.1.11

Pinging 192.168.1.11 with 32 bytes of data:

Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.11:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.1.12

Pinging 192.168.1.12 with 32 bytes of data:

Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
Reply from 192.168.1.12: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.12:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

Коммутатор S2 обновил таблицу MAC адресов и портов
Теперь он содержит MAC адреса всех устройств

```
Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    0009.7c0b.1d01    DYNAMIC     Fa0/1
   1    0060.5c2c.70d9    DYNAMIC     Fa0/1
   1    0090.0c4b.4e1e    DYNAMIC     Fa0/2
   1    00e0.b035.e635    DYNAMIC     Fa0/1
```

В ARP-кэше компьютера PC-B появилась запись MAC адресов всех устройств 

```
C:\>arp -a
  Internet Address      Physical Address      Type
  192.168.1.1           0060.5c2c.70d9        dynamic
  192.168.1.11          00e0.b035.e635        dynamic
  192.168.1.12          0001.6499.5073        dynamic
```

