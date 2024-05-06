# 720dp-swa

## Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [IP Name Servers](#ip-name-servers)
  - [NTP](#ntp)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
  - [RADIUS Server](#radius-server)
  - [AAA Server Groups](#aaa-server-groups)
  - [AAA Authentication](#aaa-authentication)
  - [AAA Authorization](#aaa-authorization)
  - [AAA Accounting](#aaa-accounting)
- [Management Security](#management-security)
  - [Management Security Summary](#management-security-summary)
  - [Management Security SSL Profiles](#management-security-ssl-profiles)
  - [SSL profile agni-server Certificates Summary](#ssl-profile-agni-server-certificates-summary)
  - [Management Security Device Configuration](#management-security-device-configuration)
- [Monitoring](#monitoring)
  - [TerminAttr Daemon](#terminattr-daemon)
  - [Logging](#logging)
  - [SFlow](#sflow)
- [VLANs](#vlans)
  - [VLANs Summary](#vlans-summary)
  - [VLANs Device Configuration](#vlans-device-configuration)
- [Interfaces](#interfaces)
  - [Interface Profiles](#interface-profiles)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [VLAN Interfaces](#vlan-interfaces)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
- [802.1X Port Security](#8021x-port-security)
  - [802.1X Summary](#8021x-summary)
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
| Management1 | oob_management | oob | MGMT | 192.168.224.107/24 | 192.168.224.1 |

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
   ip address 192.168.224.107/24
```

### IP Name Servers

#### IP Name Servers Summary

| Name Server | VRF | Priority |
| ----------- | --- | -------- |
| 1.1.1.1 | default | - |
| 8.8.8.8 | default | - |
| 1.1.1.1 | MGMT | - |
| 8.8.8.8 | MGMT | - |

#### IP Name Servers Device Configuration

```eos
ip name-server vrf default 1.1.1.1
ip name-server vrf MGMT 1.1.1.1
ip name-server vrf default 8.8.8.8
ip name-server vrf MGMT 8.8.8.8
```

### NTP

#### NTP Summary

##### NTP Servers

| Server | VRF | Preferred | Burst | iBurst | Version | Min Poll | Max Poll | Local-interface | Key |
| ------ | --- | --------- | ----- | ------ | ------- | -------- | -------- | --------------- | --- |
| time.google.com | default | True | - | True | - | - | - | - | - |

#### NTP Device Configuration

```eos
!
ntp server time.google.com prefer iburst
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

### RADIUS Server

- Dynamic Authorization is enabled with SSL profile agni-server

#### RADIUS Server Hosts

| VRF | RADIUS Servers | Timeout | Retransmit |
| --- | -------------- | ------- | ---------- |
| default | radsec.beta.agni.arista.io tls ssl-profile agni-server | - | - |

#### RADIUS Server Device Configuration

```eos
!
radius-server dynamic-authorization tls ssl-profile agni-server
radius-server host radsec.beta.agni.arista.io tls ssl-profile agni-server
```

### AAA Server Groups

#### AAA Server Groups Summary

| Server Group Name | Type  | VRF | IP address |
| ------------------| ----- | --- | ---------- |
| agni-server-group | radius | default | radsec.beta.agni.arista.io tls |

#### AAA Server Groups Device Configuration

```eos
!
aaa group server radius agni-server-group
   server radsec.beta.agni.arista.io tls
```

### AAA Authentication

#### AAA Authentication Summary

| Type | Sub-type | User Stores |
| ---- | -------- | ---------- |

#### AAA Authentication Device Configuration

```eos
aaa authentication dot1x default group radius
!
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

### AAA Accounting

#### AAA Accounting Summary

| Type | Commands | Record type | Group | Logging |
| ---- | -------- | ----------- | ----- | ------- |
| Dot1x - Default  | - | start-stop | radius | - |

#### AAA Accounting Device Configuration

```eos
aaa accounting dot1x default start-stop group radius
```

## Management Security

### Management Security Summary

| Settings | Value |
| -------- | ----- |

### Management Security SSL Profiles

| SSL Profile Name | TLS protocol accepted | Certificate filename | Key filename | Cipher List | CRLs |
| ---------------- | --------------------- | -------------------- | ------------ | ----------- | ---- |
| agni-server | - | 720dp-swa.pem | 720dp-swa.key | - | - |

### SSL profile agni-server Certificates Summary

| Trust Certificates | Requirement | Policy | System |
| ------------------ | ----------- | ------ | ------ |
| radsec_ca_certificate.pem | - | - | - |

### Management Security Device Configuration

```eos
!
management security
   ssl profile agni-server
      trust certificate radsec_ca_certificate.pem
      certificate 720dp-swa.pem key 720dp-swa.key
```

## Monitoring

### TerminAttr Daemon

#### TerminAttr Daemon Summary

| CV Compression | CloudVision Servers | VRF | Authentication | Smash Excludes | Ingest Exclude | Bypass AAA |
| -------------- | ------------------- | --- | -------------- | -------------- | -------------- | ---------- |
| gzip | apiserver.cv-staging.corp.arista.io:443 | - | token-secure,/tmp/cv-onboarding-token | ale,flexCounter,hardware,kni,pulse,strata | - | True |

#### TerminAttr Daemon Device Configuration

```eos
!
daemon TerminAttr
   exec /usr/bin/TerminAttr -cvaddr=apiserver.cv-staging.corp.arista.io:443 -cvauth=token-secure,/tmp/cv-onboarding-token -disableaaa -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -taillogs
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

sFlow Sample Rate: 500

sFlow Polling Interval: 5

sFlow is enabled.

#### SFlow Device Configuration

```eos
!
sflow sample 500
sflow polling-interval 5
sflow destination 127.0.0.1
sflow run
```

## VLANs

### VLANs Summary

| VLAN ID | Name | Trunk Groups |
| ------- | ---- | ------------ |
| 224 | Public_Internet | - |

### VLANs Device Configuration

```eos
!
vlan 224
   name Public_Internet
```

## Interfaces

### Interface Profiles

#### Interface Profiles Summary

- PORT_PROFILE

#### Interface Profiles Device Configuration

```eos
!
interface profile PORT_PROFILE
   command description Standard POE Port
   command switchport mode access
   command switchport access vlan 224
```

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |
| Ethernet3 |  - | access | - | - | - | - |

*Inherited from Port-Channel Interface

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet3
   switchport
   dot1x pae authenticator
   dot1x port-control auto
   dot1x host-mode multi-host authenticated
```

### VLAN Interfaces

#### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan224 | Public_Internet | default | - | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | VRRP | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ---- | ------ | ------- |
| Vlan224 |  default  |  192.168.224.108/24  |  -  |  -  |  -  |  -  |  -  |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan224
   description Public_Internet
   no shutdown
   ip address 192.168.224.108/24
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
| default | 172.16.1.0/24 | 192.168.224.104 | - | 1 | - | - | - |
| MGMT | 0.0.0.0/0 | 192.168.224.1 | - | 1 | - | - | - |
| MGMT | 172.16.1.0/24 | 192.168.224.104 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route 0.0.0.0/0 192.168.224.1
ip route 172.16.1.0/24 192.168.224.104
ip route vrf MGMT 0.0.0.0/0 192.168.224.1
ip route vrf MGMT 172.16.1.0/24 192.168.224.104
```

## 802.1X Port Security

### 802.1X Summary

#### 802.1X Global

| System Auth Control | Protocol LLDP Bypass | Dynamic Authorization |
| ------------------- | -------------------- | ----------------------|
| True | True | True |

#### 802.1X Interfaces

| Interface | PAE Mode | State | Phone Force Authorized | Reauthentication | Auth Failure Action | Host Mode | Mac Based Auth | Eapol |
| --------- | -------- | ------| ---------------------- | ---------------- | ------------------- | --------- | -------------- | ------ |
| Ethernet3 | authenticator | auto | - | - | - | multi-host | - | - |

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
interface Ethernet1-2
  description Arista_AP
  !poe priority critical
  poe reboot action maintain
  poe link down action maintain
  poe shutdown action power-off
  !poe limit 30.00 watts
!
interface Ethernet1-23
  profile PORT_PROFILE
!
interface Ethernet24
  description Public_Internet
  switchport mode access
  switchport access vlan 224
!
monitor connectivity
  no shutdown
  !
  host google_dns
      ip 8.8.8.8
      icmp echo size 36
  !
  host cvaas
      url https://www.cv-staging.corp.arista.io
      icmp echo size 36
!
daemon AristaCloudGateway
 exec /usr/bin/acg 
 option AGNI_API_TOKEN value eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJvcmdJRCI6IkU2NTA5NGJiYi1kZDBiLTQ2OWYtOTMyMS1mMTkzYjE3M2U2YTciLCJ0b2tlbklEIjoiRURDT08zVUpNMDYyVVM3Mk9BMEVFRyIsImlzcyI6IkFHTkkiLCJhdWQiOiJBQ0cgRGV2aWNlIFRva2VuIiwiZXhwIjoiMjEyMy0wNC0wN1QwMDo0NjowNi4yODY1ODY5NzRaIiwiaWF0IjoiMjAyNC0wNC0zMFQwMDo0NjowNi4yODY1ODg2NThaIiwic2NvcGVzIjpbImlkZW50aXR5LmNsaWVudC5wcm9maWxlIiwiaWRlbnRpdHkuY2xpZW50LnByb2ZpbGUudXBkYXRlIiwiYWNnIl0sImF0dHJzIjp7ImFjZ0RldmljZUlEIjoiNzg3MjcwYjktNWY3ZC00YThjLTliMjUtNDQxYTc3YjUxYmMwIiwiY2x1c3RlciI6ImJldGEiLCJjbHVzdGVyVVJMIjoiaHR0cHM6Ly9iZXRhLmFnbmkuYXJpc3RhLmlvIiwidG9rZW5OYW1lIjoiQ2xvdWQgR2F0ZXdheS0xIn19.yyMCJkgfwCGqdxj9zX-k7EksoqSjuj4ULXDaI56EtFUbl6I7Jbo0ENGo5ZlvHwx-1TLy6tRVTpqQ-hP7VljnOA
 no shutdown
!
match-list input prefix-ipv4 DVR_PREFIXES
  match prefix-ipv4 192.168.224.0/24
match-list input prefix-ipv4 CAMERA_PREFIXES
  match prefix-ipv4 172.16.1.0/24
match-list input prefix-ipv4 INTRANET_PREFIXES
  match prefix-ipv4 192.168.224.0/24

!
router segment-security
  vrf default
      segment SEGMENT_CAMERA
        definition
            match prefix-ipv4 CAMERA_PREFIXES
      !
      segment SEGMENT_DVR
        definition
            match prefix-ipv4 DVR_PREFIXES
      segment SEGMENT_INTRANET
        definition
            match prefix-ipv4 INTRANET_PREFIXES
      !
!
router segment-security
  vrf default
      segment SEGMENT_CAMERA
        policies
            from SEGMENT_DVR policy policy-forward-all
      !
      segment SEGMENT_DVR
        policies
            from SEGMENT_DVR policy policy-forward-all
      segment SEGMENT_INTRANET
        policies
            from SEGMENT_INTRANET policy policy-forward-all
!
router segment-security
    no segment policy policy-drop-all default
!
router segment-security
    vrf default
        segment SEGMENT_CAMERA
            policies
                from SEGMENT_CAMERA policy policy-drop-all
!
router segment-security
    no shutdown
!

```
