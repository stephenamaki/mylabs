# switch-mgmt

## Table of Contents

- [VLANs](#vlans)
  - [VLANs Summary](#vlans-summary)
  - [VLANs Device Configuration](#vlans-device-configuration)
- [Interfaces](#interfaces)
  - [Interface Profiles](#interface-profiles)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [VLAN Interfaces](#vlan-interfaces)
- [Routing](#routing)
  - [Static Routes](#static-routes)
- [EOS CLI Device Configuration](#eos-cli-device-configuration)

## VLANs

### VLANs Summary

| VLAN ID | Name | Trunk Groups |
| ------- | ---- | ------------ |
| 100 | VLAN_100 | - |
| 101 | VLAN_101 | - |
| 255 | VLAN_255 | - |

### VLANs Device Configuration

```eos
!
vlan 100
   name VLAN_100
!
vlan 101
   name VLAN_101
!
vlan 255
   name VLAN_255
```

## Interfaces

### Interface Profiles

#### Interface Profiles Summary

- PORT_PROFILE

#### Interface Profiles Device Configuration

```eos
!
interface profile PORT_PROFILE
   command description LAB Endpoints
   command switchport mode trunk
   command switchport trunk native vlan 100
   command spanning-tree portfast
```

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |
| Ethernet1 |  gwy-rtr | trunk | - | - | - | - |

*Inherited from Port-Channel Interface

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description gwy-rtr
   no shutdown
   switchport mode trunk
   switchport
```

### VLAN Interfaces

#### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan100 | LAB devices | default | - | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | VRRP | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ---- | ------ | ------- |
| Vlan100 |  default  |  172.16.1.250/24  |  -  |  -  |  -  |  -  |  -  |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan100
   description LAB devices
   no shutdown
   ip address 172.16.1.250/24
   ip address 172.100.100.250/24 secondary
   ip address 10.255.255.250/24 secondary
```

## Routing

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| default | 0.0.0.0/0 | 172.16.1.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route 0.0.0.0/0 172.16.1.1
```

## EOS CLI Device Configuration

```eos
!
!
interface Ethernet2-8
  profile PORT_PROFILE
interface Ethernet2
  description Cloud1
interface Ethernet8
  description ZTPSserver

```
