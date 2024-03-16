# TRANSIT2

## Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [IP Name Servers](#ip-name-servers)
  - [NTP](#ntp)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
  - [AAA Authorization](#aaa-authorization)
- [DHCP Server](#dhcp-server)
  - [DHCP Servers Summary](#dhcp-servers-summary)
  - [DHCP Server Configuration](#dhcp-server-configuration)
  - [DHCP Server Interfaces](#dhcp-server-interfaces)
- [Monitoring](#monitoring)
  - [TerminAttr Daemon](#terminattr-daemon)
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
- [BFD](#bfd)
  - [Router BFD](#router-bfd)
- [Filters](#filters)
  - [Prefix-lists](#prefix-lists)
  - [Route-maps](#route-maps)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | oob_management | oob | MGMT | 172.16.1.242/24 | 172.16.1.1 |

##### IPv6

| Management Interface | Description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management1 | oob_management | oob | MGMT | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management1
   description oob_management
   no shutdown
   vrf MGMT
   ip address 172.16.1.242/24
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

## DHCP Server

### DHCP Servers Summary

| DHCP Server Enabled | VRF | IPv4 DNS Domain | IPv6 DNS Domain |
| ------------------- | --- | --------------- | --------------- |
| True | default | arista.com | - |

#### VRF default DHCP Server

##### Subnets

| Subnet | Name | DNS Servers | Default Gateway | Lease Time | Ranges |
| ------ | ---- | ----------- | --------------- | ---------- | ------ |
| 10.252.251.0/24 | - | 8.8.8.8, 4.2.2.2 | 10.252.251.1 | 1 days, 0 hours, 0 minutes | 10.252.251.2-10.252.251.255 |
| 10.252.252.0/24 | - | 8.8.8.8, 4.2.2.2 | 10.252.252.1 | 1 days, 0 hours, 0 minutes | 10.252.252.2-10.252.252.255 |
| 10.252.253.0/24 | - | 8.8.8.8, 4.2.2.2 | 10.252.253.1 | 1 days, 0 hours, 0 minutes | 10.252.253.2-10.252.253.255 |
| 10.252.254.0/24 | - | 8.8.8.8, 4.2.2.2 | 10.252.254.1 | 1 days, 0 hours, 0 minutes | 10.252.254.2-10.252.254.255 |

### DHCP Server Configuration

```eos
!
dhcp server
   dns domain name ipv4 arista.com
   !
   subnet 10.252.251.0/24
      !
      range 10.252.251.2 10.252.251.255
      dns server 8.8.8.8 4.2.2.2
      lease time 1 days 0 hours 0 minutes
      default-gateway 10.252.251.1
   !
   subnet 10.252.252.0/24
      !
      range 10.252.252.2 10.252.252.255
      dns server 8.8.8.8 4.2.2.2
      lease time 1 days 0 hours 0 minutes
      default-gateway 10.252.252.1
   !
   subnet 10.252.253.0/24
      !
      range 10.252.253.2 10.252.253.255
      dns server 8.8.8.8 4.2.2.2
      lease time 1 days 0 hours 0 minutes
      default-gateway 10.252.253.1
   !
   subnet 10.252.254.0/24
      !
      range 10.252.254.2 10.252.254.255
      dns server 8.8.8.8 4.2.2.2
      lease time 1 days 0 hours 0 minutes
      default-gateway 10.252.254.1
```

### DHCP Server Interfaces

| Interface name | DHCP IPv4 | DHCP IPv6 |
| -------------- | --------- | --------- |
| Ethernet1 | True | False |
| Ethernet2 | True | False |
| Ethernet3 | True | False |
| Ethernet4 | True | False |

## Monitoring

### TerminAttr Daemon

#### TerminAttr Daemon Summary

| CV Compression | CloudVision Servers | VRF | Authentication | Smash Excludes | Ingest Exclude | Bypass AAA |
| -------------- | ------------------- | --- | -------------- | -------------- | -------------- | ---------- |
| gzip | apiserver.cv-staging.corp.arista.io:443 | MGMT | token-secure,/tmp/cv-onboarding-token | ale,flexCounter,hardware,kni,pulse,strata | - | True |

#### TerminAttr Daemon Device Configuration

```eos
!
daemon TerminAttr
   exec /usr/bin/TerminAttr -cvaddr=apiserver.cv-staging.corp.arista.io:443 -cvauth=token-secure,/tmp/cv-onboarding-token -cvvrf=MGMT -disableaaa -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -taillogs
   no shutdown
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
| Ethernet5 |  DC-RR2_TRANSIT2 | access | 255 | - | - | - |
| Ethernet7 | Portchannel_Trunk_TO_TRANSIT1_Ethernet7 | *trunk | *all | *- | *- | 1 |
| Ethernet8 | Portchannel_Trunk_TO_TRANSIT1_Ethernet8 | *trunk | *all | *- | *- | 1 |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | S-1A_TRANSIT2 | routed | - | 10.252.251.1/24 | default | - | False | - | - |
| Ethernet2 | S-2A_TRANSIT2 | routed | - | 10.252.252.1/24 | default | - | False | - | - |
| Ethernet3 | S-2B_TRANSIT2 | routed | - | 10.252.253.1/24 | default | - | False | - | - |
| Ethernet4 | S-3A_TRANSIT2 | routed | - | 10.252.254.1/24 | default | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description S-1A_TRANSIT2
   no shutdown
   no switchport
   ip address 10.252.251.1/24
   dhcp server ipv4
!
interface Ethernet2
   description S-2A_TRANSIT2
   no shutdown
   no switchport
   ip address 10.252.252.1/24
   dhcp server ipv4
!
interface Ethernet3
   description S-2B_TRANSIT2
   no shutdown
   no switchport
   ip address 10.252.253.1/24
   dhcp server ipv4
!
interface Ethernet4
   description S-3A_TRANSIT2
   no shutdown
   no switchport
   ip address 10.252.254.1/24
   dhcp server ipv4
!
interface Ethernet5
   description DC-RR2_TRANSIT2
   no shutdown
   switchport access vlan 255
   switchport mode access
   switchport
   spanning-tree portfast
!
interface Ethernet7
   description Portchannel_Trunk_TO_TRANSIT1_Ethernet7
   no shutdown
   channel-group 1 mode active
!
interface Ethernet8
   description Portchannel_Trunk_TO_TRANSIT1_Ethernet8
   no shutdown
   channel-group 1 mode active
```

### Port-Channel Interfaces

#### Port-Channel Interfaces Summary

##### L2

| Interface | Description | Type | Mode | VLANs | Native VLAN | Trunk Group | LACP Fallback Timeout | LACP Fallback Mode | MLAG ID | EVPN ESI |
| --------- | ----------- | ---- | ---- | ----- | ----------- | ------------| --------------------- | ------------------ | ------- | -------- |
| Port-Channel1 | Portchannel_Trunk_TO_TRANSIT1 | switched | trunk | all | - | - | - | - | - | - |

#### Port-Channel Interfaces Device Configuration

```eos
!
interface Port-Channel1
   description Portchannel_Trunk_TO_TRANSIT1
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
| Loopback0 | Transit_Loopback | default | 10.255.0.12/32 |

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
   ip address 10.255.0.12/32
```

### VLAN Interfaces

#### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan255 | Transit_VLAN | default | - | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | VRRP | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ---- | ------ | ------- |
| Vlan255 |  default  |  10.255.255.242/24  |  -  |  -  |  -  |  -  |  -  |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan255
   description Transit_VLAN
   no shutdown
   ip address 10.255.255.242/24
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

ASN Notation: asplain

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65200 | 10.255.0.12 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 16 ecmp 16 |

#### Router BGP Peer Groups

##### TRANSIT_ROUTING

| Settings | Value |
| -------- | ----- |
| BFD | True |
| Send community | all |
| Maximum routes | 12000 |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 10.255.255.2 | 65000 | default | - | Inherited from peer group TRANSIT_ROUTING | Inherited from peer group TRANSIT_ROUTING | - | Inherited from peer group TRANSIT_ROUTING | - | - | - | - |
| 10.255.255.241 | 65100 | default | - | Inherited from peer group TRANSIT_ROUTING | Inherited from peer group TRANSIT_ROUTING | - | Inherited from peer group TRANSIT_ROUTING | - | - | - | - |

#### Router BGP Device Configuration

```eos
!
router bgp 65200
   router-id 10.255.0.12
   maximum-paths 16 ecmp 16
   no bgp default ipv4-unicast
   neighbor TRANSIT_ROUTING peer group
   neighbor TRANSIT_ROUTING description Routing_to_TRANSIT2
   neighbor TRANSIT_ROUTING bfd
   neighbor TRANSIT_ROUTING password 7 <removed>
   neighbor TRANSIT_ROUTING send-community
   neighbor TRANSIT_ROUTING maximum-routes 12000
   neighbor 10.255.255.2 peer group TRANSIT_ROUTING
   neighbor 10.255.255.2 remote-as 65000
   neighbor 10.255.255.2 description DC-RR2
   neighbor 10.255.255.241 peer group TRANSIT_ROUTING
   neighbor 10.255.255.241 remote-as 65100
   neighbor 10.255.255.241 description TRANSIT1
   redistribute connected route-map RM-CONN-2-BGP
   !
   address-family ipv4
      neighbor TRANSIT_ROUTING activate
```

## BFD

### Router BFD

#### Router BFD Singlehop Summary

| Interval | Minimum RX | Multiplier |
| -------- | ---------- | ---------- |
| 300 | 300 | 3 |

#### Router BFD Multihop Summary

| Interval | Minimum RX | Multiplier |
| -------- | ---------- | ---------- |
| 300 | 300 | 3 |

#### Router BFD Device Configuration

```eos
!
router bfd
   interval 300 min-rx 300 multiplier 3 default
   multihop interval 300 min-rx 300 multiplier 3
```

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### SITE-CONNECTED-ROUTES

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.251.0.0/16 le 32 |
| 20 | permit 10.252.0.0/16 le 32 |

##### TRANSIT_LOOPBACKS

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.255.0.0/27 eq 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list SITE-CONNECTED-ROUTES
   seq 10 permit 10.251.0.0/16 le 32
   seq 20 permit 10.252.0.0/16 le 32
!
ip prefix-list TRANSIT_LOOPBACKS
   seq 10 permit 10.255.0.0/27 eq 32
```

### Route-maps

#### Route-maps Summary

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list TRANSIT_LOOPBACKS | - | - | - |
| 20 | permit | ip address prefix-list SITE-CONNECTED-ROUTES | - | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list TRANSIT_LOOPBACKS
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
