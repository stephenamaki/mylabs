# TEST

## Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [IP Name Servers](#ip-name-servers)
  - [NTP](#ntp)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
  - [AAA Authorization](#aaa-authorization)
- [Management Security](#management-security)
  - [Management Security Summary](#management-security-summary)
  - [Management Security Device Configuration](#management-security-device-configuration)
- [DHCP Server](#dhcp-server)
  - [DHCP Server Interfaces](#dhcp-server-interfaces)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
  - [Internal VLAN Allocation Policy Device Configuration](#internal-vlan-allocation-policy-device-configuration)
- [VLANs](#vlans)
  - [VLANs Summary](#vlans-summary)
  - [VLANs Device Configuration](#vlans-device-configuration)
- [Interfaces](#interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [Port-Channel Interfaces](#port-channel-interfaces)
  - [Loopback Interfaces](#loopback-interfaces)
  - [VLAN Interfaces](#vlan-interfaces)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
  - [Router BGP](#router-bgp)
- [Filters](#filters)
  - [Prefix-lists](#prefix-lists)
  - [Route-maps](#route-maps)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
- [EOS CLI Device Configuration](#eos-cli-device-configuration)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | oob_management | oob | MGMT | 172.16.1.246/24 | 172.16.1.1 |

##### IPv6

| Management Interface | description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management1 | oob_management | oob | MGMT | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management1
   description oob_management
   no shutdown
   vrf MGMT
   ip address 172.16.1.246/24
```

### IP Name Servers

#### IP Name Servers Summary

| Name Server | VRF | Priority |
| ----------- | --- | -------- |
| 1.1.1.1 | MGMT | - |
| 8.8.8.8 | MGMT | - |

#### IP Name Servers Device Configuration

```eos
ip name-server vrf MGMT 1.1.1.1
ip name-server vrf MGMT 8.8.8.8
```

### NTP

#### NTP Summary

##### NTP Servers

| Server | VRF | Preferred | Burst | iBurst | Version | Min Poll | Max Poll | Local-interface | Key |
| ------ | --- | --------- | ----- | ------ | ------- | -------- | -------- | --------------- | --- |
| time.google.com | MGMT | True | - | True | - | - | - | - | - |

#### NTP Device Configuration

```eos
!
ntp server vrf MGMT time.google.com prefer iburst
```

### Management API HTTP

#### Management API HTTP Summary

| HTTP | HTTPS | Default Services |
| ---- | ----- | ---------------- |
| False | True | - |

#### Management API VRF Access

| VRF Name | IPv4 ACL | IPv6 ACL |
| -------- | -------- | -------- |
| MGMT | - | - |

#### Management API HTTP Device Configuration

```eos
!
management api http-commands
   protocol https
   no shutdown
   !
   vrf MGMT
      no shutdown
```

## Authentication

### Local Users

#### Local Users Summary

| User | Privilege | Role | Disabled | Shell |
| ---- | --------- | ---- | -------- | ----- |
| admin | 15 | network-admin | False | - |
| ansible | 15 | network-admin | False | - |

#### Local Users Device Configuration

```eos
!
username admin privilege 15 role network-admin nopassword
username ansible privilege 15 role network-admin secret sha512 <removed>
```

### AAA Authorization

#### AAA Authorization Summary

| Type | User Stores |
| ---- | ----------- |
| Exec | local |

Authorization for configuration commands is disabled.

#### AAA Authorization Device Configuration

```eos
aaa authorization exec default local
!
```

## Management Security

### Management Security Summary

| Settings | Value |
| -------- | ----- |
| Common password encryption key | True |

### Management Security Device Configuration

```eos
!
management security
   password encryption-key common
```

## DHCP Server

### DHCP Server Interfaces

| Interface name | DHCP IPv4 | DHCP IPv6 |
| -------------- | --------- | --------- |
| Ethernet1 | True | False |
| Ethernet2 | True | False |
| Ethernet3 | True | False |
| Ethernet4 | True | False |
| Ethernet5 | True | False |

## Spanning Tree

### Spanning Tree Summary

STP mode: **mstp**

### Spanning Tree Device Configuration

```eos
!
spanning-tree mode mstp
```

## Internal VLAN Allocation Policy

### Internal VLAN Allocation Policy Summary

| Policy Allocation | Range Beginning | Range Ending |
| ------------------| --------------- | ------------ |
| ascending | 1006 | 1199 |

### Internal VLAN Allocation Policy Device Configuration

```eos
!
vlan internal order ascending range 1006 1199
```

## VLANs

### VLANs Summary

| VLAN ID | Name | Trunk Groups |
| ------- | ---- | ------------ |
| 255 | Transit_VLAN | - |

### VLANs Device Configuration

```eos
!
vlan 255
   name Transit_VLAN
```

## Interfaces

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |
| Ethernet5 |  DC-RR1_TRANSIT1 | access | - | - | - | - |
| Ethernet7 | Portchannel_Trunk_TO_TRANSIT2_Ethernet7 | *trunk | *all | *- | *- | 1 |
| Ethernet8 | Portchannel_Trunk_TO_TRANSIT2_Ethernet8 | *trunk | *all | *- | *- | 1 |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | S_1A_TRANSIT1 Test1 | routed | - | 10.251.251.1/24 | default | - | False | - | - |
| Ethernet2 | S_2A_TRANSIT1 | routed | - | 10.251.252.1/24 | default | - | False | - | - |
| Ethernet3 | S_2B_TRANSIT1 | routed | - | 10.251.253.1/24 | default | - | False | - | - |
| Ethernet4 | S_3A_TRANSIT1 | routed | - | 10.251.254.1/24 | default | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description S_1A_TRANSIT1 Test1
   no shutdown
   no switchport
   ip address 10.251.251.1/24
   dhcp server ipv4
!
interface Ethernet2
   description S_2A_TRANSIT1
   no shutdown
   no switchport
   ip address 10.251.252.1/24
   dhcp server ipv4
!
interface Ethernet3
   description S_2B_TRANSIT1
   no shutdown
   no switchport
   ip address 10.251.253.1/24
   dhcp server ipv4
!
interface Ethernet4
   description S_3A_TRANSIT1
   no shutdown
   no switchport
   ip address 10.251.254.1/24
   dhcp server ipv4
!
interface Ethernet5
   description DC-RR1_TRANSIT1
   no shutdown
   switchport mode access
   switchport
   dhcp server ipv4
!
interface Ethernet7
   description Portchannel_Trunk_TO_TRANSIT2_Ethernet7
   no shutdown
   channel-group 1 mode active
!
interface Ethernet8
   description Portchannel_Trunk_TO_TRANSIT2_Ethernet8
   no shutdown
   channel-group 1 mode active
```

### Port-Channel Interfaces

#### Port-Channel Interfaces Summary

##### L2

| Interface | Description | Type | Mode | VLANs | Native VLAN | Trunk Group | LACP Fallback Timeout | LACP Fallback Mode | MLAG ID | EVPN ESI |
| --------- | ----------- | ---- | ---- | ----- | ----------- | ------------| --------------------- | ------------------ | ------- | -------- |
| Port-Channel1 | Portchannel_Trunk_TO_TRANSIT2 | switched | trunk | all | - | - | - | - | - | - |

#### Port-Channel Interfaces Device Configuration

```eos
!
interface Port-Channel1
   description Portchannel_Trunk_TO_TRANSIT2
   no shutdown
   switchport
   switchport trunk allowed vlan all
   switchport mode trunk
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | Transit_Loopback | default | 10.255.0.11/32 |

##### IPv6

| Interface | Description | VRF | IPv6 Address |
| --------- | ----------- | --- | ------------ |
| Loopback0 | Transit_Loopback | default | - |

#### Loopback Interfaces Device Configuration

```eos
!
interface Loopback0
   description Transit_Loopback
   no shutdown
   ip address 10.255.0.11/32
```

### VLAN Interfaces

#### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan255 | Transit_VLAN | default | - | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | VRRP | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ---- | ------ | ------- |
| Vlan255 |  default  |  10.255.255.251/24  |  -  |  -  |  -  |  -  |  -  |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan255
   description Transit_VLAN
   no shutdown
   ip address 10.255.255.251/24
```

## Routing

### Service Routing Protocols Model

Multi agent routing protocol model enabled

```eos
!
service routing protocols model multi-agent
```

### IP Routing

#### IP Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | True |
| MGMT | False |

#### IP Routing Device Configuration

```eos
!
ip routing
no ip routing vrf MGMT
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| MGMT | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| MGMT | 0.0.0.0/0 | 172.16.1.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 172.16.1.1
```

### Router BGP

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65100 | 10.255.0.11 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |

#### Router BGP Peer Groups

##### TRANSIT_ROUTING

| Settings | Value |
| -------- | ----- |
| BFD | True |
| Send community | all |
| Maximum routes | 12000 |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- |
| 10.255.255.1 | 65000 | default | - | Inherited from peer group TRANSIT_ROUTING | Inherited from peer group TRANSIT_ROUTING | - | Inherited from peer group TRANSIT_ROUTING | - | - | - |
| 10.255.255.252 | 65200 | default | - | Inherited from peer group TRANSIT_ROUTING | Inherited from peer group TRANSIT_ROUTING | - | Inherited from peer group TRANSIT_ROUTING | - | - | - |

#### Router BGP Device Configuration

```eos
!
router bgp 65100
   router-id 10.255.0.11
   no bgp default ipv4-unicast
   neighbor TRANSIT_ROUTING peer group
   neighbor TRANSIT_ROUTING description Routing_to_TRANSIT2
   neighbor TRANSIT_ROUTING bfd
   neighbor TRANSIT_ROUTING password 7 <removed>
   neighbor TRANSIT_ROUTING send-community
   neighbor TRANSIT_ROUTING maximum-routes 12000
   neighbor 10.255.255.1 peer group TRANSIT_ROUTING
   neighbor 10.255.255.1 remote-as 65000
   neighbor 10.255.255.252 peer group TRANSIT_ROUTING
   neighbor 10.255.255.252 remote-as 65200
   redistribute connected route-map RM-CONN-2-BGP
   !
   address-family ipv4
      neighbor TRANSIT_ROUTING activate
```

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### PL-LOOPBACKS-EVPN-OVERLAY

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.255.0.0/27 eq 32 |

##### SITE-CONNECTED-ROUTES

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.251.0.0/16 le 32 |
| 20 | permit 10.252.0.0/16 le 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.255.0.0/27 eq 32
!
ip prefix-list SITE-CONNECTED-ROUTES
   seq 10 permit 10.251.0.0/16 le 32
   seq 20 permit 10.252.0.0/16 le 32
```

### Route-maps

#### Route-maps Summary

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | - | - | - |
| 20 | permit | ip address prefix-list SITE-CONNECTED-ROUTES | - | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
!
route-map RM-CONN-2-BGP permit 20
   match ip address prefix-list SITE-CONNECTED-ROUTES
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| MGMT | disabled |

### VRF Instances Device Configuration

```eos
!
vrf instance MGMT
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
  subnet 10.251.251.0/24
      range 10.251.251.2 10.251.251.255
      default-gateway 10.251.251.1
  !
  subnet 10.251.252.0/24
      range 10.251.252.2 10.251.252.255
      default-gateway 10.251.252.1
  !
  subnet 10.251.253.0/24
      range 10.251.253.2 10.251.253.255
      default-gateway 10.251.253.1
  !
  subnet 10.251.254.0/24
      range 10.251.254.2 10.251.254.255
      default-gateway 10.251.254.1
!
```
