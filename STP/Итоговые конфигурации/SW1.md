# SW1

```
Current configuration : 1495 bytes
 !
 version 12.2
 no service timestamps log datetime msec
 no service timestamps debug datetime msec
 service password-encryption
 !
 hostname SW1
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
 switchport mode trunk
 switchport nonegotiate
 !
 interface FastEthernet0/2
 switchport mode trunk
 switchport nonegotiate
 !
 interface FastEthernet0/3
 switchport mode trunk
 switchport nonegotiate
 !
 interface FastEthernet0/4
 switchport mode trunk
 switchport nonegotiate
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
 ip address 192.168.1.1 255.255.255.0
 !
 banner motd ^CUnauthorized access is prohibited^C
 !
 !
 !
 line con 0
 password 7 0822455D0A16
 logging synchronous
 login
 !
 line vty 0 4
 password 7 0822455D0A16
 login
 line vty 5 15
 password 7 0822455D0A16
 login
 !
 !
 !
 !
 end
```

