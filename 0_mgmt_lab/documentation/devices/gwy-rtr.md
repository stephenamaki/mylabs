# gwy-rtr

## Table of Contents

- [DHCP Server](#dhcp-server)
  - [DHCP Server Interfaces](#dhcp-server-interfaces)
- [Interfaces](#interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
- [Routing](#routing)
  - [Static Routes](#static-routes)
- [ACL](#acl)
  - [IP Access-lists](#ip-access-lists)
- [EOS CLI Device Configuration](#eos-cli-device-configuration)

## DHCP Server

### DHCP Server Interfaces

| Interface name | DHCP IPv4 | DHCP IPv6 |
| -------------- | --------- | --------- |
| Ethernet2.100 | True | False |

## Interfaces

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |

*Inherited from Port-Channel Interface

##### Encapsulation Dot1q Interfaces

| Interface | Description | Type | Vlan ID | Dot1q VLAN Tag |
| --------- | ----------- | -----| ------- | -------------- |
| Ethernet2.100 | LAN_to_LAB_Devices | l3dot1q | - | 100 |

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | Public_Internet | routed | - | 192.168.224.104/24 | default | - | False | - | - |
| Ethernet2.100 | LAN_to_LAB_Devices | l3dot1q | - | 172.16.1.1/24 | default | - | False | - | - |

##### IP NAT: Source Dynamic

| Interface | Access List | NAT Type | Pool Name | Priority | Comment |
| --------- | ----------- | -------- | --------- | -------- | ------- |
| Ethernet1 | NAT_ACL | overload | - | 0 | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description Public_Internet
   no shutdown
   no switchport
   ip address 192.168.224.104/24
   ip nat source dynamic access-list NAT_ACL overload
   !
   mac-address 00:00:00:22:41:04

!
interface Ethernet2
   description LAN_to_LAB_Devices
   no shutdown
   no switchport
   !
   mac-address 00:00:00:01:00:01

!
interface Ethernet2.100
   description LAN_to_LAB_Devices
   no shutdown
   encapsulation dot1q vlan 100
   ip address 172.16.1.1/24
   ip address 172.100.100.1/24 secondary
   ip address 10.255.255.254/24 secondary
   dhcp server ipv4
   !
   arp aging timeout 60

```

## Routing

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| default | 0.0.0.0/0 | 192.168.224.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route 0.0.0.0/0 192.168.224.1
```

## ACL

### IP Access-lists

#### IP Access-lists Device Configuration

```eos
!
ip access-list NAT_ACL
   10 deny ip 10.0.0.0/8 192.168.224.0/24
   20 deny ip 172.16.1.0/24 192.168.224.0/24
   30 deny ip 172.100.100.0/24 192.168.224.0/24
   40 deny ip 10.255.255.0/24 192.168.224.0/24
   50 permit ip any any
```

## EOS CLI Device Configuration

```eos
!
!
dhcp server
 lease time ipv4 1 days 0 hours 0 minutes
 dns domain name ipv4 arista.com
 dns server ipv4 8.8.8.8 4.2.2.2
 !
 subnet 172.16.1.0/24
    range 172.16.1.165 172.16.1.245
    default-gateway 172.16.1.1
    tftp server file tftp://172.16.1.2/bootstrap.py
monitor connectivity
  no shutdown
  !
  host google_dns
      ip 8.8.8.8
      icmp echo size 36
  !
  host ubuntu_linux
      ip 192.168.224.101
      icmp echo size 36
  !
  host host_001
      ip 172.16.1.11
      icmp echo size 36
  !
  host host_002
      ip 172.16.1.12
      icmp echo size 36
  !
  host host_003
      ip 172.16.1.101
      icmp echo size 36
  !
  host host_004
      ip 172.16.1.102
      icmp echo size 36
  !
  host host_005
      ip 172.16.1.103
      icmp echo size 36
  !
  host host_006
      ip 172.16.1.104
      icmp echo size 36
  !
  host host_007
      ip 172.16.1.151
      icmp echo size 36
  !
  host host_008
      ip 172.16.1.152
      icmp echo size 36
  !
  host host_009
      ip 172.16.1.241
      icmp echo size 36
  !
  host host_010
      ip 172.16.1.242
      icmp echo size 36
  !
  host host_011
      ip 172.16.1.243
      icmp echo size 36
  !
  host host_012
      ip 172.16.1.244
      icmp echo size 36
  !
  host host_013
      ip 172.16.1.245
      icmp echo size 36
  !
  host host_014
      ip 172.16.1.21
      icmp echo size 36
  !
  host host_015
      ip 172.16.1.22
      icmp echo size 36
  !
  host host_016
      ip 172.16.1.111
      icmp echo size 36
  !
  host host_017
      ip 172.16.1.112
      icmp echo size 36
  !
  host host_018
      ip 172.16.1.113
      icmp echo size 36
  !
  host host_019
      ip 172.16.1.114
      icmp echo size 36
  !
  host host_020
      ip 172.16.1.161
      icmp echo size 36
  !
  host host_021
      ip 172.16.1.162
      icmp echo size 36
!

```
