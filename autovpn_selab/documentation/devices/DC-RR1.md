# DC-RR1

## Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [IP Name Servers](#ip-name-servers)
  - [NTP](#ntp)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
  - [AAA Authorization](#aaa-authorization)
- [Monitoring](#monitoring)
  - [TerminAttr Daemon](#terminattr-daemon)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
  - [Internal VLAN Allocation Policy Device Configuration](#internal-vlan-allocation-policy-device-configuration)
- [Interfaces](#interfaces)
  - [DPS Interfaces](#dps-interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [VXLAN Interface](#vxlan-interface)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
  - [Router BGP](#router-bgp)
- [BFD](#bfd)
  - [Router BFD](#router-bfd)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
  - [Router Path-selection](#router-path-selection)
- [STUN](#stun)
  - [STUN Server](#stun-server)
  - [STUN Device Configuration](#stun-device-configuration)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | oob_management | oob | MGMT | 172.16.1.11/24 | 172.16.1.1 |

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
   ip address 172.16.1.11/24
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

## Spanning Tree

### Spanning Tree Summary

STP mode: **none**

### Spanning Tree Device Configuration

```eos
!
spanning-tree mode none
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

## Interfaces

### DPS Interfaces

#### DPS Interfaces Summary

| Interface | IP address | Shutdown | MTU | Flow tracker(s) | TCP MSS Ceiling |
| --------- | ---------- | -------- | --- | --------------- | --------------- |
| Dps1 | - | False | - |  |  |

#### DPS Interfaces Device Configuration

```eos
!
interface Dps1
   no shutdown
```

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet2 | SITE_TRAFFIC | routed | - | 10.240.241.1/24 | SITE_TRAFFIC | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet2
   description SITE_TRAFFIC
   no shutdown
   no switchport
   vrf SITE_TRAFFIC
   ip address 10.240.241.1/24
```

### VXLAN Interface

#### VXLAN Interface Summary

| Setting | Value |
| ------- | ----- |
| Source Interface | Loopback0 |
| UDP port | 4789 |

##### VRF to VNI and Multicast Group Mappings

| VRF | VNI | Multicast Group |
| ---- | --- | --------------- |
| default | 101 | - |
| SITE_TRAFFIC | 250 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description VTEP_Termination
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vrf default vni 101
   vxlan vrf SITE_TRAFFIC vni 250
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
| 65000 | 10.255.0.1 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 16 ecmp 16 |

#### Router BGP Listen Ranges

| Prefix | Peer-ID Include Router ID | Peer Group | Peer-Filter | Remote-AS | VRF |
| ------ | ------------------------- | ---------- | ----------- | --------- | --- |
| 10.255.0.0/24 | - | AUTOVPN_EDGES | - | 65000 | default |

#### Router BGP Peer Groups

##### AUTOVPN_EDGES

| Settings | Value |
| -------- | ----- |
| Remote AS | 65000 |
| Route Reflector Client | Yes |
| Source | Loopback0 |
| BFD | True |
| Send community | extended |
| Maximum routes | 12000 |

##### TRANSIT_ROUTING

| Settings | Value |
| -------- | ----- |
| BFD | True |
| Send community | all |
| Maximum routes | 12000 |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 10.255.255.241 | 65100 | default | - | Inherited from peer group TRANSIT_ROUTING | Inherited from peer group TRANSIT_ROUTING | - | Inherited from peer group TRANSIT_ROUTING | - | - | - | - |
| 10.240.241.251 | 65101 | SITE_TRAFFIC | - | - | - | - | True | - | - | - |

#### Router BGP EVPN Address Family

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| AUTOVPN_EDGES | True | default |

#### Router BGP Path-Selection Address Family

##### Path-Selection Peer Groups

| Peer Group | Activate |
| ---------- | -------- |
| AUTOVPN_EDGES | True |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute |
| --- | ------------------- | ------------ |
| SITE_TRAFFIC | 10.255.0.1:1 | connected<br>static |

#### Router BGP Device Configuration

```eos
!
router bgp 65000
   router-id 10.255.0.1
   maximum-paths 16 ecmp 16
   no bgp default ipv4-unicast
   bgp listen range 10.255.0.0/24 peer-group AUTOVPN_EDGES remote-as 65000
   neighbor AUTOVPN_EDGES peer group
   neighbor AUTOVPN_EDGES remote-as 65000
   neighbor AUTOVPN_EDGES update-source Loopback0
   neighbor AUTOVPN_EDGES description Routing_to_Edge_Routers
   neighbor AUTOVPN_EDGES route-reflector-client
   neighbor AUTOVPN_EDGES bfd
   neighbor AUTOVPN_EDGES password 7 <removed>
   neighbor AUTOVPN_EDGES send-community extended
   neighbor AUTOVPN_EDGES maximum-routes 12000
   neighbor TRANSIT_ROUTING peer group
   neighbor TRANSIT_ROUTING description Routing_to_TRANSIT2
   neighbor TRANSIT_ROUTING bfd
   neighbor TRANSIT_ROUTING password 7 <removed>
   neighbor TRANSIT_ROUTING send-community
   neighbor TRANSIT_ROUTING maximum-routes 12000
   neighbor 10.255.255.241 peer group TRANSIT_ROUTING
   neighbor 10.255.255.241 remote-as 65100
   neighbor 10.255.255.241 description TRANSIT1
   !
   address-family evpn
      neighbor AUTOVPN_EDGES activate
   !
   address-family ipv4
      no neighbor AUTOVPN_EDGES activate
      neighbor TRANSIT_ROUTING activate
   !
   address-family path-selection
      bgp additional-paths receive
      bgp additional-paths send any
      neighbor AUTOVPN_EDGES activate
      neighbor AUTOVPN_EDGES additional-paths receive
      neighbor AUTOVPN_EDGES additional-paths send any
   !
   vrf SITE_TRAFFIC
      rd 10.255.0.1:1
      route-target import evpn 65000:1
      route-target export evpn 65000:1
      neighbor 10.240.241.251 remote-as 65101
      neighbor 10.240.241.251 password 7 <removed>
      neighbor 10.240.241.251 bfd
      redistribute connected
      redistribute static
      !
      address-family ipv4
         neighbor 10.240.241.251 activate
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

### Router Path-selection

#### Router Path-selection Summary

| Setting | Value |
| ------  | ----- |
| Dynamic peers source | STUN |

#### Path Groups

##### Path Group TRANSIT1

| Setting | Value |
| ------  | ----- |
| Path Group ID | - |
| IPSec profile | AUTOVPN_TUNNEL |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1 | - |  |

#### Load-balance Policies

| Policy Name | Jitter (ms) | Latency (ms) | Loss Rate (%) | Path Groups (priority) | Lowest Hop Count |
| ----------- | ----------- | ------------ | ------------- | ---------------------- | ---------------- |
| dps-lb-policy-default | - | - | - | TRANSIT1 (1) | False |

#### DPS Policies

##### DPS Policy dps-policy-default

| Rule ID | Application profile | Load-balance policy |
| ------- | ------------------- | ------------------- |
| Default Match | - | dps-lb-policy-default |

#### VRFs Configuration

| VRF name | DPS policy |
| -------- | ---------- |
| default | dps-policy-default |
| SITE_TRAFFIC | dps-policy-default |

#### Router Path-selection Device Configuration

```eos
!
router path-selection
   peer dynamic source stun
   !
   path-group TRANSIT1
      ipsec profile AUTOVPN_TUNNEL
      !
      local interface Ethernet1
   !
   load-balance policy dps-lb-policy-default
      path-group TRANSIT1
   !
   policy dps-policy-default
      default-match
         load-balance dps-lb-policy-default
   !
   vrf default
      path-selection-policy dps-policy-default
   !
   vrf SITE_TRAFFIC
      path-selection-policy dps-policy-default
```

## STUN

### STUN Server

| Server Local Interfaces | Bindings Timeout (s) | SSL Profile | SSL Connection Lifetime | Port |
| ----------------------- | -------------------- | ----------- | ----------------------- | ---- |
| Ethernet1 | - | - | - | 3478 |

### STUN Device Configuration

```eos
!
stun
   server
      local-interface Ethernet1
```
