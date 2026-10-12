# Проектная работа - Построение сети с нуля

## Топология

![alt text](topology.png)

## Таблица адресации

![alt text](address.png)

## Таблица VLAN

![alt text](vlan.png)

## Часть 1. Настройка SW2

```
Switch>enable
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SW2
SW2(config)#vlan 10
SW2(config-vlan)#name ofice_1
SW2(config-vlan)#exit
SW2(config)#vlan 20
SW2(config-vlan)#name ofice_2
SW2(config-vlan)#exit
SW2(config)#interface range fastEthernet0/1-20
SW2(config-if-range)#switchport mode access
SW2(config-if-range)#switchport access vlan 10
SW2(config-if-range)#spanning-tree portfast

SW2(config-if-range)#no shutdown
SW2(config-if-range)#exit
SW2(config)#interface fastEthernet0/24
SW2(config-if)#switchport mode trunk

SW2(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to up

SW2(config-if)#switchport trunk allowed vlan 10,20
SW2(config-if)#no shutdown
SW2(config-if)#exit
SW2(config)#spanning-tree vlan 10,20
SW2(config)#cpd run
             ^
% Invalid input detected at '^' marker.
	
SW2(config)#cdp run
SW2(config)#lldp run
SW2(config)#end
SW2#
%SYS-5-CONFIG_I: Configured from console by console

SW2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
SW2#
```

## Часть 2. Настройка SW3

```
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SW3
SW3(config)#vlan 10
SW3(config-vlan)#name ofice_1
SW3(config-vlan)#exit
SW3(config)#vlan 20
SW3(config-vlan)#name ofice_2
SW3(config-vlan)#exit
SW3(config)#interface range fastEthernet0/1-20
SW3(config-if-range)#switchport mode access
SW3(config-if-range)#switchport access vlan 20
SW3(config-if-range)#spanning-tree portfast
SW3(config-if-range)#no shutdown
SW3(config-if-range)#exit
SW3(config)#interface fastEthernet0/24
SW3(config-if)#switchport mode trunk

SW3(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to up
switchport trunk allowed vlan 10,20
SW3(config-if)#no shutdown
SW3(config-if)#exi
SW3(config)#spanning-tree vlan 10,20
SW3(config)#cdp run
SW3(config)#lldp run
SW3(config)#end
SW3#
%SYS-5-CONFIG_I: Configured from console by console

SW3#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
SW3#
```

## Часть 3. Настройка SW_L3 (VLAN, SVIs, DHCP, OSPF, NTP, SSH)


```
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SW_L3
SW_L3(config)#vlan 10
SW_L3(config-vlan)#name ofice_1
SW_L3(config-vlan)#exit
SW_L3(config)#vlan 20
SW_L3(config-vlan)#name ofice_2
SW_L3(config-vlan)#exit
SW_L3(config)#interface fastEthernet0/9
SW_L3(config-if)#switchport mode access
SW_L3(config-if)#switchport access vlan 10
SW_L3(config-if)#spanning-tree portfast
%Warning: portfast should only be enabled on ports connected to a single
host. Connecting hubs, concentrators, switches, bridges, etc... to this
interface  when portfast is enabled, can cause temporary bridging loops.
Use with CAUTION

%Portfast has been configured on FastEthernet0/9 but will only
have effect when the interface is in a non-trunking mode.
SW_L3(config-if)#no shutdown
SW_L3(config-if)#exit
SW_L3(config)#interface fastEthernet0/23
SW_L3(config-if)#switchport mode trunk
Command rejected: An interface whose trunk encapsulation is "Auto" can not be configured to "trunk" mode.
SW_L3(config-if)#switchport trunk allowed vlan 10,20
SW_L3(config-if)#no shutdown
SW_L3(config-if)#exit
SW_L3(config)#interface fastEthernet0/24
SW_L3(config-if)#switchport mode trunk
Command rejected: An interface whose trunk encapsulation is "Auto" can not be configured to "trunk" mode.
SW_L3(config-if)#switchport trunk allowed vlan 10,20
SW_L3(config-if)#no shutdown
SW_L3(config-if)#exit
SW_L3(config)#interface Vlan10
SW_L3(config-if)#
%LINK-5-CHANGED: Interface Vlan10, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan10, changed state to up

SW_L3(config-if)#ip address 192.168.10.254 255.255.255.0
SW_L3(config-if)#no shutdown
SW_L3(config-if)#exit
SW_L3(config)#ip routing
SW_L3(config)#interface Vlan20
SW_L3(config-if)#
%LINK-5-CHANGED: Interface Vlan20, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan20, changed state to up
 ip address 192.168.20.254 255.255.255.0
SW_L3(config-if)#no shutdown
SW_L3(config-if)#exit
SW_L3(config)#ip dhcp pool VLAN10
SW_L3(dhcp-config)#network 192.168.10.0 255.255.255.0
SW_L3(dhcp-config)#default-router 192.168.10.254
SW_L3(dhcp-config)#dns-server 192.168.10.250
SW_L3(dhcp-config)#exit
SW_L3(config)#ip dhcp pool VLAN20
SW_L3(dhcp-config)#network 192.168.20.0 255.255.255.0
SW_L3(dhcp-config)#default-router 192.168.20.254
SW_L3(dhcp-config)#dns-server 192.168.10.250
SW_L3(dhcp-config)#exit
SW_L3(config)#ntp master 
SW_L3(config)#exit
SW_L3#
%SYS-5-CONFIG_I: Configured from console by console

SW_L3#clock set 12:00:00 12 Oct 2026
SW_L3#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW_L3(config)#ip domain-name example.com
SW_L3(config)#crypto key generate rsa general-keys modulus 1024
The name for the keys will be: SW_L3.example.com

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Oct 12 12:3:12.802: %SSH-5-ENABLED: SSH 1.99 has been enabled
SW_L3(config)#username admin secret cisco
SW_L3(config)#line vty 0 15
SW_L3(config-line)#transport input ssh
SW_L3(config-line)#login local
SW_L3(config-line)#exit
SW_L3(config)#ip ssh version 2
SW_L3(config)#exit
SW_L3#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW_L3(config)#cdp run
SW_L3(config)#lldp run
SW_L3(config)#exit
SW_L3#
%SYS-5-CONFIG_I: Configured from console by console

SW_L3#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
SW_L3#
SW_L3(config)#router ospf 1
SW_L3(config-router)#router-id 3.3.3.3
SW_L3(config-router)#network 192.168.10.0 0.0.0.255 area 0
SW_L3(config-router)#network 192.168.20.0 0.0.0.255 area 0 
SW_L3(config-router)#network 203.0.113.1 0.0.0.0 area 0
SW_L3(config-router)#network 198.51.100.1 0.0.0.0 area 0
SW_L3(config-router)#end
SW_L3#
%SYS-5-CONFIG_I: Configured from console by console

SW_L3#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
SW_L3#
```

## Часть 4. Настройка IPS1 (первый роутер, первый провайдер)

```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname IPS1
IPS1(config)#interface GigabitEthernet0/0/1
IPS1(config-if)#ip address 10.10.10.1 255.255.255.252
IPS1(config-if)#no shutdown

IPS1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
exit

IPS1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.

IPS1(config)#interface GigabitEthernet0/0/0
IPS1(config-if)#ip address 203.0.113.1 255.255.255.252
IPS1(config-if)#no shutdown

IPS1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up
exit
IPS1(config)#exit
IPS1#
%SYS-5-CONFIG_I: Configured from console by console

IPS1#router ospf 1
            ^
% Invalid input detected at '^' marker.
	
IPS1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IPS1(config)#router ospf 1
IPS1(config-router)# router-id 1.1.1.1
IPS1(config-router)#network 203.0.113.0 0.0.0.255 area 0
IPS1(config-router)#default-information originate
IPS1(config-router)#exit
IPS1(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2
IPS1(config)#access-list 1 permit 192.168.10.0 0.0.0.255
IPS1(config)#access-list 1 permit 192.168.20.0 0.0.0.255
IPS1(config)#interface GigabitEthernet0/0/0
IPS1(config-if)#ip nat outside
IPS1(config-if)#exit
IPS1(config)#interface GigabitEthernet0/0/1
IPS1(config-if)#ip nat inside
IPS1(config-if)#exit
IPS1(config)#ip nat inside source list 1 interface GigabitEthernet0/0/0 overload
IPS1(config)#cdp run
IPS1(config)#lldp run
IPS1(config)#ntp server 192.168.10.254 prefer
                                       ^
% Invalid input detected at '^' marker.
	
IPS1(config)#ntp server 192.168.10.254 
IPS1(config)#ip domain-name example.com
IPS1(config)#crypto key generate rsa general-keys modulus 1024
The name for the keys will be: IPS1.example.com

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 6:40:43.756: %SSH-5-ENABLED: SSH 1.99 has been enabled
IPS1(config)#username admin secret cisco
IPS1(config)#ip ssh version 2
IPS1(config)#line vty 0 15
IPS1(config-line)#transport input ssh
IPS1(config-line)#login local
IPS1(config-line)#exit
IPS1(config)#end
IPS1#
%SYS-5-CONFIG_I: Configured from console by console
copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
IPS1#
```

## Часть 5. Настройка IPS2 (второй роутер, второй провайдер)

```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname IPS2
IPS2(config)#interface GigabitEthernet0/0/1
IPS2(config-if)#ip address 10.10.20.1 255.255.255.252
IPS2(config-if)#no shutdown

IPS2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
exit
IPS2(config)#interface GigabitEthernet0/0/0
IPS2(config-if)#ip address 198.51.100.1 255.255.255.252
IPS2(config-if)#no shutdown

IPS2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up
exit
IPS2(config)#ip route 0.0.0.0 0.0.0.0 198.51.100.2
IPS2(config)#access-list 1 permit 192.168.10.0 0.0.0.255
IPS2(config)#access-list 1 permit 192.168.20.0 0.0.0.255
IPS2(config)#router ospf 1 
IPS2(config-router)#router-id 2.2.2.2
IPS2(config-router)#network 198.51.100.0 0.0.0.255 area 0
IPS2(config-router)#default-information originate 
IPS2(config-router)#exit
IPS2(config)#interface GigabitEthernet0/0/0
IPS2(config-if)#ip nat outside
IPS2(config-if)#exit
IPS2(config)#interface GigabitEthernet0/0/1
IPS2(config-if)#ip nat inside
IPS2(config-if)#exit
IPS2(config)#ip nat inside source list 1 interface GigabitEthernet0/0/0 overload
IPS2(config)#cdp run
IPS2(config)#lldp run
IPS2(config)#ntp server 192.168.10.254
IPS2(config)#ip domain-name example.com
IPS2(config)#crypto key generate rsa general-keys modulus 1024
The name for the keys will be: IPS2.example.com

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 6:45:2.502: %SSH-5-ENABLED: SSH 1.99 has been enabled
IPS2(config)#username admin secret cisco
IPS2(config)#ip ssh version 2
IPS2(config)#line vty 0 15
IPS2(config-line)#transport input ssh
IPS2(config-line)#login local
IPS2(config-line)#exit
IPS2(config)#end
IPS2#
%SYS-5-CONFIG_I: Configured from console by console
copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
IPS2#
```

## Часть 6. Настройка DNS на Server

![alt text](vlan.png)
![alt text](vlan.png)
![alt text](vlan.png)
![alt text](vlan.png)
![alt text](vlan.png)
