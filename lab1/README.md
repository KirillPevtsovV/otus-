# Лабораторная работа. Базовая настройка коммутатора 

## Топология 
![](2026-04-07_17-19-28.png)

## Таблица адресации 
| Устройство | Интерфейс | IP-адрес / префикс | 
|------------|-----------|--------------------|
| S1         | VLAN 1    | 192.168.1.2 /24    |
| PC-A       | NIC       | 192.168.1.10 /24   |

##  Задачи:

 ### Часть 1. Проверка конфигурации коммутатора по умолчанию
  
 ### Часть 2. Создание сети и настройка основных параметров устройства
  
•	Настройте базовые параметры коммутатора.

•	Настройте IP-адрес для ПК.

  ### Часть 3. Проверка сетевых подключений
  
•	Отобразите конфигурацию устройства.

•	Протестируйте сквозное соединение, отправив эхо-запрос.

•	Протестируйте возможности удаленного управления с помощью Telnet.




##  Решение:
## Часть 1:


**Шаг 1. Создание сети по топологии**

а. Соедините устройства с помощью консольного кабеля, следуя выбранной топологии.

б. Установите соединение через консольный порт.

**Вопрос:** Почему для первоначальной настройки коммутатора нужно использовать консольное подключение?  
**Ответ:** Коммутатор изначально настроен на заводе, поэтому первоначальная настройка выполняется через консольный кабель.

**Вопрос:** Можно ли подключиться к коммутатору по Telnet или SSH?  
**Ответ:** Нет, так как коммутатору еще не присвоен IP-адрес, доступ по Telnet заблокирован, и пароль для соединения не введен.



**Шаг 2. Проверка настроек коммутатора по умолчанию**

**a. Проверка настроек коммутатора по умолчанию**  
Сначала войдите в привилегированный режим:  

```
Switch>enable
```  

Затем проверьте настройки командой:  

```
Switch#show run
```

**b. Изучение текущего файла running configuration**  

- Сколько интерфейсов FastEthernet на коммутаторе 2960? - 24  
- Сколько интерфейсов Gigabit Ethernet на коммутаторе 2960? - 2  
- Каков диапазон значений vty-линий? - 0-4 и 5-15  

**c. Изучение файла загрузочной конфигурации (startup configuration)**  

Вводим команду:  

```
Switch#show startup-config
```  

Получаем ответ:  

```
startup-config is not present
```  

Это нормально, так как файл startup-config изначально отсутствует. Он появится позже, когда мы завершим настройку и скопируем running-config в startup-config.  

**d. Изучение характеристик SVI для VLAN 1**  

- Назначен ли IP-адрес сети VLAN 1?  
  Изначально IP-адрес не назначен. Его можно задать в режиме настройки интерфейса VLAN (SW(config-if)).  

- Какой MAC-адрес имеет SVI?  
  SVI — виртуальный интерфейс, поэтому у него нет MAC-адреса. Однако его можно назначить.  

- Включён ли данный интерфейс?  
  Изначально интерфейс выключен. После присвоения IP-адреса его нужно активировать командой `no shutdown`.  

**e. Изучение IP-свойств интерфейса SVI сети VLAN 1**  


``` 
    Switch#show vlan brief
    
    
    VLAN Name                             Status    Ports
    ---- -------------------------------- --------- -------------------------------
    1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
    1002 fddi-default                     active    
    1003 token-ring-default               active    
    1004 fddinet-default                  active    
    1005 trnet-default                    active 
    
    Switch#show ip interface brief


    Interface              IP-Address      OK? Method Status                Protocol 
    FastEthernet0/1        unassigned      YES manual down                  down 
    FastEthernet0/2        unassigned      YES manual down                  down 
    FastEthernet0/3        unassigned      YES manual down                  down 
    FastEthernet0/4        unassigned      YES manual down                  down 
    FastEthernet0/5        unassigned      YES manual down                  down 
    FastEthernet0/6        unassigned      YES manual down                  down 
    FastEthernet0/7        unassigned      YES manual down                  down 
    FastEthernet0/8        unassigned      YES manual down                  down 
    FastEthernet0/9        unassigned      YES manual down                  down 
    FastEthernet0/10       unassigned      YES manual down                  down 
    FastEthernet0/11       unassigned      YES manual down                  down 
    FastEthernet0/12       unassigned      YES manual down                  down 
    FastEthernet0/13       unassigned      YES manual down                  down 
    FastEthernet0/14       unassigned      YES manual down                  down 
    FastEthernet0/15       unassigned      YES manual down                  down 
    FastEthernet0/16       unassigned      YES manual down                  down 
    FastEthernet0/17       unassigned      YES manual down                  down 
    FastEthernet0/18       unassigned      YES manual down                  down 
    FastEthernet0/19       unassigned      YES manual down                  down 
    FastEthernet0/20       unassigned      YES manual down                  down 
    FastEthernet0/21       unassigned      YES manual down                  down 
    FastEthernet0/22       unassigned      YES manual down                  down 
    FastEthernet0/23       unassigned      YES manual down                  down 
    FastEthernet0/24       unassigned      YES manual down                  down 
    GigabitEthernet0/1     unassigned      YES manual down                  down 
    GigabitEthernet0/2     unassigned      YES manual down                  down 
    Vlan1                  unassigned      YES manual administratively down down

```

 
Мы видим, что все порты находятся в состоянии "down", а VLAN1 отключен администратором.

Подсоединим кабель Ethernet от компьютера PC-A к порту 1 на коммутаторе и проверим IP-настройки интерфейса SVI сети VLAN 1. Дождемся, пока коммутатор и ПК согласуют параметры скорости и дуплекса.

- Какие данные вы видите? 


```
    Switch#show ip interface brief


    Interface              IP-Address      OK? Method Status                Protocol 
    FastEthernet0/1        unassigned      YES manual down                  down 
    FastEthernet0/2        unassigned      YES manual down                  down 
    FastEthernet0/3        unassigned      YES manual down                  down 
    FastEthernet0/4        unassigned      YES manual down                  down 
    FastEthernet0/5        unassigned      YES manual down                  down 
    FastEthernet0/6        unassigned      YES manual up                    up 
    FastEthernet0/7        unassigned      YES manual down                  down 
    FastEthernet0/8        unassigned      YES manual down                  down 
    FastEthernet0/9        unassigned      YES manual down                  down 
    FastEthernet0/10       unassigned      YES manual down                  down 
    FastEthernet0/11       unassigned      YES manual down                  down 
    FastEthernet0/12       unassigned      YES manual down                  down 
    FastEthernet0/13       unassigned      YES manual down                  down 
    FastEthernet0/14       unassigned      YES manual down                  down 
    FastEthernet0/15       unassigned      YES manual down                  down 
    FastEthernet0/16       unassigned      YES manual down                  down 
    FastEthernet0/17       unassigned      YES manual down                  down 
    FastEthernet0/18       unassigned      YES manual down                  down 
    FastEthernet0/19       unassigned      YES manual down                  down 
    FastEthernet0/20       unassigned      YES manual down                  down 
    FastEthernet0/21       unassigned      YES manual down                  down 
    FastEthernet0/22       unassigned      YES manual down                  down 
    FastEthernet0/23       unassigned      YES manual down                  down 
    FastEthernet0/24       unassigned      YES manual down                  down 
    GigabitEthernet0/1     unassigned      YES manual down                  down 
    GigabitEthernet0/2     unassigned      YES manual down                  down 
    Vlan1                  unassigned      YES manual administratively down down
```

Мы видим, что шестой порт коммутатора активен. Это значит, что подключение к ПК выполнено корректно.


**g.	Изучим сведения о версии ОС Cisco IOS на коммутаторе.**

 Введем комманду:
```
        Switch#show version 
```

Видим, что версия ОС Cisco IOS - 15.0(2)SE4 

 - Как называется файл образа системы?
    
    Файл образа системы C2960-LANBASEK9-M

**h.	Изучите свойства по умолчанию интерфейса FastEthernet, который используется компьютером PC-A.**

 Введем комманду:    
```
        Switch#show interface f0/6
```

Видим, что интерфейс включен.

Если интерфейс отключен, выполните следующие шаги:

1. Войдите в режим конфигурации:

   ```conf t```

2. Перейдите к настройке интерфейса командой:

   ```int f0/6```

3. Включите интерфейс:

   ```no shutdown```

После этого вы сможете проверить IP-настройки и убедиться, что интерфейс активен.
  

**i.	Изучите флеш-память**
    
 Введите команду:
```
     Switch#show flash
```
Имя образа Cisco IOS: "2960-lanbasek9-mz.150-2.SE4.bin"
## Часть 2:

**Шаг 1. Настройте базовые параметры коммутатора**

**a. Настройка в режиме глобальной конфигурации switch**

```
Switch(config)# no ip domain-lookup  
Switch(config)# hostname SW1  
Switch(config)# service password-encryption  
Switch(config)# enable secret class  
Switch(config)# banner motd # Unauthorized access is strictly prohibited. #
```

**b. Назначим IP-адрес интерфейсу SVI на коммутаторе**

```
SW1(config)# interface vlan 1  
SW1(config-if)# ip address 192.168.1.2 255.255.255.0  
SW1(config-if)# no shutdown
```

**c. Настройка консоли: ограничение доступа паролем и отключение прерывания команд консольными сообщениями**

```
SW1(config-line)# password cisco  
SW1(config-line)# login  
SW1(config-line)# logging synchronous
```

**d. Настройка виртуальных каналов для удаленного управления через Telnet**

```
SW1(config)# line vty 0 4  
SW1(config-line)# password cisco  
SW1(config-line)# login  
SW1(config-line)# transport input telnet
```

Команда `login` активирует запрос пароля при входе в текущую ветку настройки конфигурации коммутатора.

**Шаг 2. Настройте IP-адрес на компьютере PC-A**

На логической схеме нашей схемы заходим в настройки PC0 и во вкладке CONFIG прописываем STATIC\FastEternet сетевые настройки:

        IP: 192.168.1.10
        NM: 255.255.255.0

-----------------------------------------------------

## Часть 3:

**Шаг 1. Просмотрим конфигурацию коммутатора.**

```
    sw1#show run
Building config uration...

Current configuration : 1265 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname sw1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
!
!
no ip domain-lookup
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
 ip address 192.168.1.2 255.255.255.0
!
banner motd ^C Unauthorized access is strictly prohibited. ^C
!
!
!
line con 0
!
line vty 0 4
 password 7 0822455D0A16
 login
 transport input telnet
line vty 5 15
 login
!
!
!
!
end
```
**Шаг 2. Протестируйте сквозное соединение, отправив эхо-запрос.**
```
 C:\>ping 192.168.1.10

Pinging 192.168.1.10 with 32 bytes of data:

Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
Reply from 192.168.1.10: bytes=32 time=5ms TTL=128
Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
Reply from 192.168.1.10: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.1.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 5ms, Average = 1ms

C:\>ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.2: bytes=32 time=9ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.2:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 9ms, Average = 3ms
```

  


