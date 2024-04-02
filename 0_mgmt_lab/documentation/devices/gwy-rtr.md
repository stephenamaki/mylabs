# gwy-rtr

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
  - [DHCP Server Interfaces](#dhcp-server-interfaces)
- [Monitoring](#monitoring)
  - [TerminAttr Daemon](#terminattr-daemon)
  - [Logging](#logging)
  - [SFlow](#sflow)
- [Interfaces](#interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
- [ACL](#acl)
  - [IP Access-lists](#ip-access-lists)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
- [EOS CLI Device Configuration](#eos-cli-device-configuration)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | oob_management | oob | MGMT | 192.168.224.103/24 | 192.168.224.1 |

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
   ip address 192.168.224.103/24
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
| default | - | - |
| MGMT | - | - |

#### Management API HTTP Device Configuration

```eos
!
management api http-commands
   protocol https
   no shutdown
   !
   vrf default
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

### DHCP Server Interfaces

| Interface name | DHCP IPv4 | DHCP IPv6 |
| -------------- | --------- | --------- |
| Ethernet2 | True | False |

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

### Logging

#### Logging Servers and Features Summary

| Type | Level |
| -----| ----- |
| Monitor | debugging |

#### Logging Servers and Features Device Configuration

```eos
!
logging monitor debugging
```

### SFlow

#### SFlow Summary

| VRF | SFlow Source | SFlow Destination | Port |
| --- | ------------ | ----------------- | ---- |
| default | - | 127.0.0.1 | 6343 |
| default | Management1 | - | - |

sFlow Sample Rate: 500

sFlow Polling Interval: 5

sFlow is enabled.

#### SFlow Device Configuration

```eos
!
sflow sample 500
sflow polling-interval 5
sflow destination 127.0.0.1
sflow source-interface Management1
sflow run
```

## Interfaces

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | Public_Internet | routed | - | 192.168.224.104/24 | default | - | False | - | - |
| Ethernet2 | LAN_to_LAB_Devices | routed | - | 172.16.1.1/24 | default | - | False | - | - |

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
   ip address 172.16.1.1/24
   dhcp server ipv4
   !
   mac-address 00:00:00:01:00:01
   arp aging timeout 60

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
| MGMT | True |

#### IP Routing Device Configuration

```eos
!
ip routing
ip routing vrf MGMT
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
| default | 0.0.0.0/0 | 192.168.224.1 | - | 1 | - | - | - |
| MGMT | 0.0.0.0/0 | 192.168.224.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route 0.0.0.0/0 192.168.224.1
ip route vrf MGMT 0.0.0.0/0 192.168.224.1
```

## ACL

### IP Access-lists

#### IP Access-lists Device Configuration

```eos
!
ip access-list NAT_ACL
   10 deny ip 172.16.1.0/24 192.168.224.0/24
   99 permit ip any any
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| MGMT | enabled |

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
 subnet 172.16.1.0/24
    range 172.16.1.165 172.16.1.245
    default-gateway 172.16.1.1
    tftp server file tftp://192.168.224.105/bootstrap.py
    !reservations
       !mac-address 5000.0002.0000
       !   ipv4-address 172.16.1.171
       !   hostname 0-test-01
       !
       !mac-address 5002.0002.0000
       !   ipv4-address 172.16.1.172
       !   hostname 0-test-02
       !
       !mac-address 5003.0003.0000
       !   ipv4-address 172.16.1.173
       !   hostname 0-test-03
       !

monitor connectivity
  no shutdown
  !
  host google_dns
      ip 8.8.8.8
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
