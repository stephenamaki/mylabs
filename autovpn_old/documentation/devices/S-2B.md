# S-2B

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
- [IP Security](#ip-security)
  - [IKE policies](#ike-policies)
  - [Security Association policies](#security-association-policies)
  - [IPSec profiles](#ipsec-profiles)
  - [Key controller](#key-controller)
  - [IP Security Device Configuration](#ip-security-device-configuration)
- [Interfaces](#interfaces)
  - [DPS Interfaces](#dps-interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [Loopback Interfaces](#loopback-interfaces)
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
  - [STUN Client](#stun-client)
  - [STUN Device Configuration](#stun-device-configuration)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | oob_management | oob | MGMT | 172.16.1.103/24 | 172.16.1.1 |

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
   ip address 172.16.1.103/24
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

## IP Security

### IKE policies

| Policy name | IKE lifetime | Encryption | DH group | Local ID |
| ----------- | ------------ | ---------- | -------- | -------- |
| IKE_AUTOVPN | - | - | - | 10.255.0.103 |

### Security Association policies

| Policy name | ESP Integrity | ESP Encryption | PFS DH Group |
| ----------- | ------------- | -------------- | ------------ |
| SA_AUTOVPN | - | - | - |

### IPSec profiles

| Profile name | IKE policy | SA policy | Connection | DPD Interval | DPD Time | DPD action | Mode | Flow Parallelization |
| ------------ | ---------- | ----------| ---------- | ------------ | -------- | ---------- | ---- | -------------------- |
| AUTOVPN_TUNNEL | IKE_AUTOVPN | SA_AUTOVPN | start | - | - | - | transport | - |

### Key controller

| Profile name |
| ------------ |
| AUTOVPN_TUNNEL |

### IP Security Device Configuration

```eos
!
ip security
   !
   ike policy IKE_AUTOVPN
      local-id 10.255.0.103
   !
   sa policy SA_AUTOVPN
   !
   profile AUTOVPN_TUNNEL
      ike-policy IKE_AUTOVPN
      sa-policy SA_AUTOVPN
      connection start
      shared-key 7 <removed>
      dpd 10 50 clear
      mode transport
   !
   key controller
      profile AUTOVPN_TUNNEL
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
| Ethernet1 | TRANSIT1 | routed | - | dhcp | default | - | False | - | - |
| Ethernet2 | TRANSIT2 | routed | - | dhcp | default | - | False | - | - |
| Ethernet3 | SITE_TRAFFIC | routed | - | 10.250.253.1/24 | SITE_TRAFFIC | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description TRANSIT1
   no shutdown
   no switchport
   ip address dhcp
   dhcp client accept default-route
!
interface Ethernet2
   description TRANSIT2
   no shutdown
   no switchport
   ip address dhcp
   dhcp client accept default-route
!
interface Ethernet3
   description SITE_TRAFFIC
   no shutdown
   no switchport
   vrf SITE_TRAFFIC
   ip address 10.250.253.1/24
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | Transit_Loopback | default | 10.255.0.103/32 |

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
   ip address 10.255.0.103/32
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
| SITE_TRAFFIC | True |

#### IP Routing Device Configuration

```eos
!
ip routing
no ip routing vrf MGMT
ip routing vrf SITE_TRAFFIC
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| MGMT | false |
| SITE_TRAFFIC | false |

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
| 65000 | 10.255.0.103 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 16 ecmp 16 |

#### Router BGP Peer Groups

##### PATHFINDERS

| Settings | Value |
| -------- | ----- |
| Remote AS | 65000 |
| Source | Loopback0 |
| BFD | True |
| Send community | extended |
| Maximum routes | 12000 |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 10.255.0.1 | Inherited from peer group PATHFINDERS | default | - | Inherited from peer group PATHFINDERS | Inherited from peer group PATHFINDERS | - | Inherited from peer group PATHFINDERS | - | - | - | - |
| 10.255.0.2 | Inherited from peer group PATHFINDERS | default | - | Inherited from peer group PATHFINDERS | Inherited from peer group PATHFINDERS | - | Inherited from peer group PATHFINDERS | - | - | - | - |
| 10.250.253.251 | 65103 | SITE_TRAFFIC | - | - | - | - | True | - | - | - |

#### Router BGP EVPN Address Family

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| PATHFINDERS | True | default |

#### Router BGP Path-Selection Address Family

##### Path-Selection Peer Groups

| Peer Group | Activate |
| ---------- | -------- |
| PATHFINDERS | True |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute |
| --- | ------------------- | ------------ |
| SITE_TRAFFIC | 10.255.0.103:1 | connected<br>static |

#### Router BGP Device Configuration

```eos
!
router bgp 65000
   router-id 10.255.0.103
   maximum-paths 16 ecmp 16
   no bgp default ipv4-unicast
   neighbor PATHFINDERS peer group
   neighbor PATHFINDERS remote-as 65000
   neighbor PATHFINDERS update-source Loopback0
   neighbor PATHFINDERS description Routing_to_Pathfinders
   neighbor PATHFINDERS bfd
   neighbor PATHFINDERS password 7 <removed>
   neighbor PATHFINDERS send-community extended
   neighbor PATHFINDERS maximum-routes 12000
   neighbor 10.255.0.1 peer group PATHFINDERS
   neighbor 10.255.0.1 description Headend - DC-RR1
   neighbor 10.255.0.2 peer group PATHFINDERS
   neighbor 10.255.0.2 description Headend - DC-RR2
   !
   address-family evpn
      neighbor PATHFINDERS activate
   !
   address-family ipv4
      no neighbor PATHFINDERS activate
   !
   address-family path-selection
      neighbor PATHFINDERS activate
      neighbor PATHFINDERS additional-paths receive
      neighbor PATHFINDERS additional-paths send any
   !
   vrf SITE_TRAFFIC
      rd 10.255.0.103:1
      route-target import evpn 65000:1
      route-target export evpn 65000:1
      neighbor 10.250.253.251 remote-as 65103
      neighbor 10.250.253.251 password 7 <removed>
      neighbor 10.250.253.251 description SITE_TRAFFIC
      neighbor 10.250.253.251 bfd
      redistribute connected
      redistribute static
      !
      address-family ipv4
         neighbor 10.250.253.251 activate
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
| SITE_TRAFFIC | enabled |

### VRF Instances Device Configuration

```eos
!
vrf instance MGMT
!
vrf instance SITE_TRAFFIC
```

### Router Path-selection

#### Path Groups

##### Path Group TRANSIT1

| Setting | Value |
| ------  | ----- |
| Path Group ID | - |
| IPSec profile | AUTOVPN_TUNNEL |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1 | - | PROFILE1<br>PROFILE2 |

###### Dynamic Peers Settings

| Setting | Value |
| ------  | ----- |
| IP Local | - |
| IPSec | - |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | DC-RR1 | 10.255.255.1 |
| 10.255.0.2 | DC-RR1 | 10.255.255.2 |

##### Path Group TRANSIT2

| Setting | Value |
| ------  | ----- |
| Path Group ID | - |
| IPSec profile | AUTOVPN_TUNNEL |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet2 | - | PROFILE1<br>PROFILE2 |

###### Dynamic Peers Settings

| Setting | Value |
| ------  | ----- |
| IP Local | - |
| IPSec | - |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | DC-RR1 | 10.255.255.1 |
| 10.255.0.2 | DC-RR1 | 10.255.255.2 |

#### Load-balance Policies

| Policy Name | Jitter (ms) | Latency (ms) | Loss Rate (%) | Path Groups (priority) | Lowest Hop Count |
| ----------- | ----------- | ------------ | ------------- | ---------------------- | ---------------- |
| dps-lb-policy-default | - | - | - | TRANSIT1 (1)<br>TRANSIT2 (1) | False |

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
   !
   path-group TRANSIT1
      ipsec profile AUTOVPN_TUNNEL
      !
      local interface Ethernet1
         stun server-profile PROFILE1 PROFILE2
      !
      peer dynamic
      !
      peer static router-ip 10.255.0.1
         name DC-RR1
         ipv4 address 10.255.255.1
      !
      peer static router-ip 10.255.0.2
         name DC-RR1
         ipv4 address 10.255.255.2
   !
   path-group TRANSIT2
      ipsec profile AUTOVPN_TUNNEL
      !
      local interface Ethernet2
         stun server-profile PROFILE1 PROFILE2
      !
      peer dynamic
      !
      peer static router-ip 10.255.0.1
         name DC-RR1
         ipv4 address 10.255.255.1
      !
      peer static router-ip 10.255.0.2
         name DC-RR1
         ipv4 address 10.255.255.2
   !
   load-balance policy dps-lb-policy-default
      path-group TRANSIT1
      path-group TRANSIT2
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

### STUN Client

#### Server Profiles

| Server Profile | IP address | SSL Profile | Port |
| -------------- | ---------- | ----------- | ---- |
| PROFILE1 | 10.255.255.1 | - | 3478 |
| PROFILE2 | 10.255.255.2 | - | 3478 |

### STUN Device Configuration

```eos
!
stun
   client
      server-profile PROFILE1
         ip address 10.255.255.1
      server-profile PROFILE2
         ip address 10.255.255.2
```
