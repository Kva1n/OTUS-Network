# SW1

```
Current configuration : 1444 bytes

!

version 12.2

no service timestamps log datetime msec

no service timestamps debug datetime msec

no service password-encryption

!

hostname sw1-vtp-client

!

!

!

!

vtp domain CCNA

vtp mode transparent

vtp password cisco

!

!

!

spanning-tree mode pvst

spanning-tree extend system-id

!

vlan 10

name Red

!

vlan 20

name Blue

!

vlan 30

name Yellow

!

vlan 99

name Management

!

vlan 2000

!

interface FastEthernet0/1

switchport mode dynamic desirable

!

interface FastEthernet0/2

!

interface FastEthernet0/3

switchport mode trunk

!

interface FastEthernet0/4

!

interface FastEthernet0/5

!

interface FastEthernet0/6

switchport access vlan 10

switchport mode access

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
```

