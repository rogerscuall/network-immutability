# DCX-LEAF1B

## Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [IP Name Servers](#ip-name-servers)
  - [Domain Lookup](#domain-lookup)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
  - [Enable Password](#enable-password)
- [Monitoring](#monitoring)
  - [TerminAttr Daemon](#terminattr-daemon)
- [MLAG](#mlag)
  - [MLAG Summary](#mlag-summary)
  - [MLAG Device Configuration](#mlag-device-configuration)
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
  - [VXLAN Interface](#vxlan-interface)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [Virtual Router MAC Address](#virtual-router-mac-address)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
  - [Router BGP](#router-bgp)
- [BFD](#bfd)
  - [Router BFD](#router-bfd)
- [Multicast](#multicast)
  - [IP IGMP Snooping](#ip-igmp-snooping)
- [Filters](#filters)
  - [Prefix-lists](#prefix-lists)
  - [Route-maps](#route-maps)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
- [Virtual Source NAT](#virtual-source-nat)
  - [Virtual Source NAT Summary](#virtual-source-nat-summary)
  - [Virtual Source NAT Configuration](#virtual-source-nat-configuration)
- [EOS CLI Device Configuration](#eos-cli-device-configuration)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | OOB_MANAGEMENT | oob | MGMT | 172.20.20.54/24 | 172.20.20.1 |

##### IPv6

| Management Interface | Description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management1 | OOB_MANAGEMENT | oob | MGMT | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management1
   description OOB_MANAGEMENT
   no shutdown
   vrf MGMT
   ip address 172.20.20.54/24
```

### IP Name Servers

#### IP Name Servers Summary

| Name Server | VRF | Priority |
| ----------- | --- | -------- |
| 8.8.8.8 | MGMT | - |

#### IP Name Servers Device Configuration

```eos
ip name-server vrf MGMT 8.8.8.8
```

### Domain Lookup

#### DNS Domain Lookup Summary

| Source interface | vrf |
| ---------------- | --- |
| Management1 | MGMT |

#### DNS Domain Lookup Device Configuration

```eos
ip domain lookup vrf MGMT source-interface Management1
```

### Management API HTTP

#### Management API HTTP Summary

| HTTP | HTTPS | UNIX-Socket | Default Services |
| ---- | ----- | ----------- | ---------------- |
| True | True | - | - |

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
   protocol http
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

### Enable Password

Enable password has been disabled

## Monitoring

### TerminAttr Daemon

#### TerminAttr Daemon Summary

| CV Compression | CloudVision Servers | VRF | Authentication | Smash Excludes | Ingest Exclude | Bypass AAA |
| -------------- | ------------------- | --- | -------------- | -------------- | -------------- | ---------- |
| gzip | 172.20.20.100:9910 | MGMT | token,/tmp/token | ale,flexCounter,hardware,kni,pulse,strata | - | False |

#### TerminAttr Daemon Device Configuration

```eos
!
daemon TerminAttr
   exec /usr/bin/TerminAttr -cvaddr=172.20.20.100:9910 -cvauth=token,/tmp/token -cvvrf=MGMT -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -taillogs
   no shutdown
```

## MLAG

### MLAG Summary

| Domain-id | Local-interface | Peer-address | Peer-link |
| --------- | --------------- | ------------ | --------- |
| DCX_L3_LEAF1 | Vlan4094 | 10.255.1.64 | Port-Channel4 |

Dual primary detection is disabled.

### MLAG Device Configuration

```eos
!
mlag configuration
   domain-id DCX_L3_LEAF1
   local-interface Vlan4094
   peer-address 10.255.1.64
   peer-link Port-Channel4
   reload-delay mlag 300
   reload-delay non-mlag 330
```

## Spanning Tree

### Spanning Tree Summary

STP mode: **mstp**

#### MSTP Instance and Priority

| Instance(s) | Priority |
| -------- | -------- |
| 0 | 4096 |

#### Global Spanning-Tree Settings

- Spanning Tree disabled for VLANs: **4093-4094**

### Spanning Tree Device Configuration

```eos
!
spanning-tree mode mstp
no spanning-tree vlan-id 4093-4094
spanning-tree mst 0 priority 4096
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
| 11 | VRF10_VLAN11 | - |
| 12 | VRF10_VLAN12 | - |
| 13 | VRF10_VLAN13 | - |
| 21 | VRF11_VLAN21 | - |
| 25 | VRF11_VLAN22 | - |
| 31 | VRF11_VLAN31 | - |
| 3009 | MLAG_L3_VRF_VRF10 | MLAG |
| 3010 | MLAG_L3_VRF_VRF11 | MLAG |
| 3011 | MLAG_L3_VRF_VRF12 | MLAG |
| 3401 | L2_VLAN3401 | - |
| 3402 | L2_VLAN3402 | - |
| 3403 | L2_VLAN3403 | - |
| 4093 | MLAG_L3 | MLAG |
| 4094 | MLAG | MLAG |

### VLANs Device Configuration

```eos
!
vlan 11
   name VRF10_VLAN11
!
vlan 12
   name VRF10_VLAN12
!
vlan 13
   name VRF10_VLAN13
!
vlan 21
   name VRF11_VLAN21
!
vlan 25
   name VRF11_VLAN22
!
vlan 31
   name VRF11_VLAN31
!
vlan 3009
   name MLAG_L3_VRF_VRF10
   trunk group MLAG
!
vlan 3010
   name MLAG_L3_VRF_VRF11
   trunk group MLAG
!
vlan 3011
   name MLAG_L3_VRF_VRF12
   trunk group MLAG
!
vlan 3401
   name L2_VLAN3401
!
vlan 3402
   name L2_VLAN3402
!
vlan 3403
   name L2_VLAN3403
!
vlan 4093
   name MLAG_L3
   trunk group MLAG
!
vlan 4094
   name MLAG
   trunk group MLAG
```

## Interfaces

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |
| Ethernet4 | MLAG_DCX-LEAF1A_Ethernet4 | *trunk | *- | *- | *MLAG | 4 |
| Ethernet5 | SERVER_DCX-leaf1-server1_PCI2 | *trunk | *11-12,21-22 | *4092 | *- | 5 |
| Ethernet20 | UNUSED1 | access | 50 | - | - | - |
| Ethernet21 | UNUSED1 | access | 50 | - | - | - |
| Ethernet22 | UNUSED1 | access | 50 | - | - | - |
| Ethernet23 | UNUSED1 | access | 50 | - | - | - |
| Ethernet24 | UNUSED1 | access | 50 | - | - | - |
| Ethernet25 | UNUSED1 | access | 50 | - | - | - |
| Ethernet26 | UNUSED1 | access | 50 | - | - | - |
| Ethernet27 | UNUSED1 | access | 50 | - | - | - |
| Ethernet28 | UNUSED1 | access | 50 | - | - | - |
| Ethernet29 | UNUSED1 | access | 50 | - | - | - |
| Ethernet30 | UNUSED1 | access | 50 | - | - | - |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | P2P_DCX-SPINE1_Ethernet2 | - | 10.255.255.5/31 | default | 1600 | False | - | - |
| Ethernet2 | P2P_DCX-SPINE2_Ethernet2 | - | 10.255.255.7/31 | default | 1600 | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description P2P_DCX-SPINE1_Ethernet2
   no shutdown
   mtu 1600
   no switchport
   ip address 10.255.255.5/31
!
interface Ethernet2
   description P2P_DCX-SPINE2_Ethernet2
   no shutdown
   mtu 1600
   no switchport
   ip address 10.255.255.7/31
!
interface Ethernet4
   description MLAG_DCX-LEAF1A_Ethernet4
   no shutdown
   channel-group 4 mode active
!
interface Ethernet5
   description SERVER_DCX-leaf1-server1_PCI2
   no shutdown
   channel-group 5 mode active
!
interface Ethernet20
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
!
interface Ethernet21
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
!
interface Ethernet22
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
!
interface Ethernet23
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
!
interface Ethernet24
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
!
interface Ethernet25
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
!
interface Ethernet26
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
!
interface Ethernet27
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
!
interface Ethernet28
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
!
interface Ethernet29
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
!
interface Ethernet30
   description UNUSED1
   no shutdown
   switchport access vlan 50
   switchport mode access
   switchport
```

### Port-Channel Interfaces

#### Port-Channel Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | LACP Fallback Timeout | LACP Fallback Mode | MLAG ID | EVPN ESI |
| --------- | ----------- | ---- | ----- | ----------- | ------------| --------------------- | ------------------ | ------- | -------- |
| Port-Channel4 | MLAG_DCX-LEAF1A_Port-Channel4 | trunk | - | - | MLAG | - | - | - | - |
| Port-Channel5 | PortChannel DCX-leaf1-server1 | trunk | 11-12,21-22 | 4092 | - | - | - | 5 | - |

#### Port-Channel Interfaces Device Configuration

```eos
!
interface Port-Channel4
   description MLAG_DCX-LEAF1A_Port-Channel4
   no shutdown
   switchport mode trunk
   switchport trunk group MLAG
   switchport
!
interface Port-Channel5
   description PortChannel DCX-leaf1-server1
   no shutdown
   switchport trunk native vlan 4092
   switchport trunk allowed vlan 11-12,21-22
   switchport mode trunk
   switchport
   mlag 5
   spanning-tree portfast
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | ROUTER_ID | default | 10.255.0.4/32 |
| Loopback1 | VXLAN_TUNNEL_SOURCE | default | 10.255.1.3/32 |
| Loopback10 | DIAG_VRF_VRF10 | VRF10 | 10.255.10.4/32 |
| Loopback11 | DIAG_VRF_VRF11 | VRF11 | 10.255.11.4/32 |
| Loopback12 | DIAG_VRF_VRF12 | VRF12 | 10.255.12.4/32 |

##### IPv6

| Interface | Description | VRF | IPv6 Address |
| --------- | ----------- | --- | ------------ |
| Loopback0 | ROUTER_ID | default | - |
| Loopback1 | VXLAN_TUNNEL_SOURCE | default | - |
| Loopback10 | DIAG_VRF_VRF10 | VRF10 | - |
| Loopback11 | DIAG_VRF_VRF11 | VRF11 | - |
| Loopback12 | DIAG_VRF_VRF12 | VRF12 | - |

#### Loopback Interfaces Device Configuration

```eos
!
interface Loopback0
   description ROUTER_ID
   no shutdown
   ip address 10.255.0.4/32
!
interface Loopback1
   description VXLAN_TUNNEL_SOURCE
   no shutdown
   ip address 10.255.1.3/32
!
interface Loopback10
   description DIAG_VRF_VRF10
   no shutdown
   vrf VRF10
   ip address 10.255.10.4/32
!
interface Loopback11
   description DIAG_VRF_VRF11
   no shutdown
   vrf VRF11
   ip address 10.255.11.4/32
!
interface Loopback12
   description DIAG_VRF_VRF12
   no shutdown
   vrf VRF12
   ip address 10.255.12.4/32
```

### VLAN Interfaces

#### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan11 | VRF10_VLAN11 | VRF10 | - | False |
| Vlan12 | VRF10_VLAN12 | VRF10 | - | False |
| Vlan13 | VRF10_VLAN13 | VRF10 | - | False |
| Vlan21 | VRF11_VLAN21 | VRF11 | - | False |
| Vlan25 | VRF11_VLAN22 | VRF11 | - | False |
| Vlan31 | VRF11_VLAN31 | VRF12 | - | False |
| Vlan3009 | MLAG_L3_VRF_VRF10 | VRF10 | 1600 | False |
| Vlan3010 | MLAG_L3_VRF_VRF11 | VRF11 | 1600 | False |
| Vlan3011 | MLAG_L3_VRF_VRF12 | VRF12 | 1600 | False |
| Vlan4093 | MLAG_L3 | default | 1600 | False |
| Vlan4094 | MLAG | default | 1600 | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ------ | ------- |
| Vlan11 |  VRF10  |  -  |  10.10.11.1/24  |  -  |  -  |  -  |
| Vlan12 |  VRF10  |  -  |  10.10.12.1/24  |  -  |  -  |  -  |
| Vlan13 |  VRF10  |  -  |  10.10.13.1/24  |  -  |  -  |  -  |
| Vlan21 |  VRF11  |  -  |  10.10.21.1/24  |  -  |  -  |  -  |
| Vlan25 |  VRF11  |  -  |  10.10.22.1/24  |  -  |  -  |  -  |
| Vlan31 |  VRF12  |  -  |  10.10.31.1/24  |  -  |  -  |  -  |
| Vlan3009 |  VRF10  |  10.255.1.97/31  |  -  |  -  |  -  |  -  |
| Vlan3010 |  VRF11  |  10.255.1.97/31  |  -  |  -  |  -  |  -  |
| Vlan3011 |  VRF12  |  10.255.1.97/31  |  -  |  -  |  -  |  -  |
| Vlan4093 |  default  |  10.255.1.97/31  |  -  |  -  |  -  |  -  |
| Vlan4094 |  default  |  10.255.1.65/31  |  -  |  -  |  -  |  -  |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan11
   description VRF10_VLAN11
   no shutdown
   vrf VRF10
   ip address virtual 10.10.11.1/24
!
interface Vlan12
   description VRF10_VLAN12
   no shutdown
   vrf VRF10
   ip address virtual 10.10.12.1/24
!
interface Vlan13
   description VRF10_VLAN13
   no shutdown
   vrf VRF10
   ip address virtual 10.10.13.1/24
!
interface Vlan21
   description VRF11_VLAN21
   no shutdown
   vrf VRF11
   ip address virtual 10.10.21.1/24
!
interface Vlan25
   description VRF11_VLAN22
   no shutdown
   vrf VRF11
   ip address virtual 10.10.22.1/24
!
interface Vlan31
   description VRF11_VLAN31
   no shutdown
   vrf VRF12
   ip address virtual 10.10.31.1/24
!
interface Vlan3009
   description MLAG_L3_VRF_VRF10
   no shutdown
   mtu 1600
   vrf VRF10
   ip address 10.255.1.97/31
!
interface Vlan3010
   description MLAG_L3_VRF_VRF11
   no shutdown
   mtu 1600
   vrf VRF11
   ip address 10.255.1.97/31
!
interface Vlan3011
   description MLAG_L3_VRF_VRF12
   no shutdown
   mtu 1600
   vrf VRF12
   ip address 10.255.1.97/31
!
interface Vlan4093
   description MLAG_L3
   no shutdown
   mtu 1600
   ip address 10.255.1.97/31
!
interface Vlan4094
   description MLAG
   no shutdown
   mtu 1600
   no autostate
   ip address 10.255.1.65/31
```

### VXLAN Interface

#### VXLAN Interface Summary

| Setting | Value |
| ------- | ----- |
| Source Interface | Loopback1 |
| UDP port | 4789 |
| EVPN MLAG Shared Router MAC | mlag-system-id |

##### VLAN to VNI, Flood List and Multicast Group Mappings

| VLAN | VNI | Flood List | Multicast Group |
| ---- | --- | ---------- | --------------- |
| 11 | 10011 | - | - |
| 12 | 10012 | - | - |
| 13 | 10013 | - | - |
| 21 | 10021 | - | - |
| 25 | 10025 | - | - |
| 31 | 10031 | - | - |
| 3401 | 13401 | - | - |
| 3402 | 13402 | - | - |
| 3403 | 13403 | - | - |

##### VRF to VNI and Multicast Group Mappings

| VRF | VNI | Multicast Group |
| ---- | --- | --------------- |
| VRF10 | 10 | - |
| VRF11 | 11 | - |
| VRF12 | 12 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description DCX-LEAF1B_VTEP
   vxlan source-interface Loopback1
   vxlan virtual-router encapsulation mac-address mlag-system-id
   vxlan udp-port 4789
   vxlan vlan 11 vni 10011
   vxlan vlan 12 vni 10012
   vxlan vlan 13 vni 10013
   vxlan vlan 21 vni 10021
   vxlan vlan 25 vni 10025
   vxlan vlan 31 vni 10031
   vxlan vlan 3401 vni 13401
   vxlan vlan 3402 vni 13402
   vxlan vlan 3403 vni 13403
   vxlan vrf VRF10 vni 10
   vxlan vrf VRF11 vni 11
   vxlan vrf VRF12 vni 12
```

## Routing

### Service Routing Protocols Model

Multi agent routing protocol model enabled

```eos
!
service routing protocols model multi-agent
```

### Virtual Router MAC Address

#### Virtual Router MAC Address Summary

Virtual Router MAC Address: 00:1c:73:00:00:99

#### Virtual Router MAC Address Device Configuration

```eos
!
ip virtual-router mac-address 00:1c:73:00:00:99
```

### IP Routing

#### IP Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | True |
| MGMT | False |
| VRF10 | True |
| VRF11 | True |
| VRF12 | True |

#### IP Routing Device Configuration

```eos
!
ip routing
no ip routing vrf MGMT
ip routing vrf VRF10
ip routing vrf VRF11
ip routing vrf VRF12
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| MGMT | false |
| VRF10 | false |
| VRF11 | false |
| VRF12 | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| MGMT | 0.0.0.0/0 | 172.20.20.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 172.20.20.1
```

### Router BGP

ASN Notation: asplain

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65101 | 10.255.0.4 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 4 ecmp 4 |

#### Router BGP Peer Groups

##### EVPN-OVERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | evpn |
| Source | Loopback0 |
| BFD | True |
| Ebgp multihop | 3 |
| Send community | all |
| Maximum routes | 0 (no limit) |

##### IPv4-UNDERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Send community | all |
| Maximum routes | 12000 |

##### MLAG-IPv4-UNDERLAY-PEER

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Remote AS | 65101 |
| Next-hop self | True |
| Send community | all |
| Maximum routes | 12000 |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 10.255.0.1 | 65100 | default | - | Inherited from peer group EVPN-OVERLAY-PEERS | Inherited from peer group EVPN-OVERLAY-PEERS | - | Inherited from peer group EVPN-OVERLAY-PEERS | - | - | - | - |
| 10.255.0.2 | 65100 | default | - | Inherited from peer group EVPN-OVERLAY-PEERS | Inherited from peer group EVPN-OVERLAY-PEERS | - | Inherited from peer group EVPN-OVERLAY-PEERS | - | - | - | - |
| 10.255.1.96 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | default | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |
| 10.255.255.4 | 65100 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.255.255.6 | 65100 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.255.1.96 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | VRF10 | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |
| 10.255.1.96 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | VRF11 | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |
| 10.255.1.96 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | VRF12 | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |

#### Router BGP EVPN Address Family

##### EVPN Peer Groups

| Peer Group | Activate | Route-map In | Route-map Out | Encapsulation | Next-hop-self Source Interface |
| ---------- | -------- | ------------ | ------------- | ------------- | ------------------------------ |
| EVPN-OVERLAY-PEERS | True |  - | - | default | - |

#### Router BGP VLANs

| VLAN | Route-Distinguisher | Both Route-Target | Import Route Target | Export Route-Target | Redistribute |
| ---- | ------------------- | ----------------- | ------------------- | ------------------- | ------------ |
| 11 | 10.255.0.4:10011 | 10011:10011 | - | - | learned |
| 12 | 10.255.0.4:10012 | 10012:10012 | - | - | learned |
| 13 | 10.255.0.4:10013 | 10013:10013 | - | - | learned |
| 21 | 10.255.0.4:10021 | 10021:10021 | - | - | learned |
| 25 | 10.255.0.4:10025 | 10025:10025 | - | - | learned |
| 31 | 10.255.0.4:10031 | 10031:10031 | - | - | learned |
| 3401 | 10.255.0.4:13401 | 13401:13401 | - | - | learned |
| 3402 | 10.255.0.4:13402 | 13402:13402 | - | - | learned |
| 3403 | 10.255.0.4:13403 | 13403:13403 | - | - | learned |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute | Graceful Restart |
| --- | ------------------- | ------------ | ---------------- |
| VRF10 | 10.255.0.4:10 | connected | - |
| VRF11 | 10.255.0.4:11 | connected | - |
| VRF12 | 10.255.0.4:12 | connected | - |

#### Router BGP Device Configuration

```eos
!
router bgp 65101
   router-id 10.255.0.4
   no bgp default ipv4-unicast
   maximum-paths 4 ecmp 4
   neighbor EVPN-OVERLAY-PEERS peer group
   neighbor EVPN-OVERLAY-PEERS update-source Loopback0
   neighbor EVPN-OVERLAY-PEERS bfd
   neighbor EVPN-OVERLAY-PEERS ebgp-multihop 3
   neighbor EVPN-OVERLAY-PEERS password 7 <removed>
   neighbor EVPN-OVERLAY-PEERS send-community
   neighbor EVPN-OVERLAY-PEERS maximum-routes 0
   neighbor IPv4-UNDERLAY-PEERS peer group
   neighbor IPv4-UNDERLAY-PEERS password 7 <removed>
   neighbor IPv4-UNDERLAY-PEERS send-community
   neighbor IPv4-UNDERLAY-PEERS maximum-routes 12000
   neighbor MLAG-IPv4-UNDERLAY-PEER peer group
   neighbor MLAG-IPv4-UNDERLAY-PEER remote-as 65101
   neighbor MLAG-IPv4-UNDERLAY-PEER next-hop-self
   neighbor MLAG-IPv4-UNDERLAY-PEER description DCX-LEAF1A
   neighbor MLAG-IPv4-UNDERLAY-PEER route-map RM-MLAG-PEER-IN in
   neighbor MLAG-IPv4-UNDERLAY-PEER password 7 <removed>
   neighbor MLAG-IPv4-UNDERLAY-PEER send-community
   neighbor MLAG-IPv4-UNDERLAY-PEER maximum-routes 12000
   neighbor 10.255.0.1 peer group EVPN-OVERLAY-PEERS
   neighbor 10.255.0.1 remote-as 65100
   neighbor 10.255.0.1 description DCX-SPINE1_Loopback0
   neighbor 10.255.0.2 peer group EVPN-OVERLAY-PEERS
   neighbor 10.255.0.2 remote-as 65100
   neighbor 10.255.0.2 description DCX-SPINE2_Loopback0
   neighbor 10.255.1.96 peer group MLAG-IPv4-UNDERLAY-PEER
   neighbor 10.255.1.96 description DCX-LEAF1A_Vlan4093
   neighbor 10.255.255.4 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.255.255.4 remote-as 65100
   neighbor 10.255.255.4 description DCX-SPINE1_Ethernet2
   neighbor 10.255.255.6 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.255.255.6 remote-as 65100
   neighbor 10.255.255.6 description DCX-SPINE2_Ethernet2
   redistribute connected route-map RM-CONN-2-BGP
   !
   vlan 11
      rd 10.255.0.4:10011
      route-target both 10011:10011
      redistribute learned
   !
   vlan 12
      rd 10.255.0.4:10012
      route-target both 10012:10012
      redistribute learned
   !
   vlan 13
      rd 10.255.0.4:10013
      route-target both 10013:10013
      redistribute learned
   !
   vlan 21
      rd 10.255.0.4:10021
      route-target both 10021:10021
      redistribute learned
   !
   vlan 25
      rd 10.255.0.4:10025
      route-target both 10025:10025
      redistribute learned
   !
   vlan 31
      rd 10.255.0.4:10031
      route-target both 10031:10031
      redistribute learned
   !
   vlan 3401
      rd 10.255.0.4:13401
      route-target both 13401:13401
      redistribute learned
   !
   vlan 3402
      rd 10.255.0.4:13402
      route-target both 13402:13402
      redistribute learned
   !
   vlan 3403
      rd 10.255.0.4:13403
      route-target both 13403:13403
      redistribute learned
   !
   address-family evpn
      neighbor EVPN-OVERLAY-PEERS activate
   !
   address-family ipv4
      no neighbor EVPN-OVERLAY-PEERS activate
      neighbor IPv4-UNDERLAY-PEERS activate
      neighbor MLAG-IPv4-UNDERLAY-PEER activate
   !
   vrf VRF10
      rd 10.255.0.4:10
      route-target import evpn 10:10
      route-target export evpn 10:10
      router-id 10.255.0.4
      neighbor 10.255.1.96 peer group MLAG-IPv4-UNDERLAY-PEER
      neighbor 10.255.1.96 description DCX-LEAF1A_Vlan3009
      redistribute connected route-map RM-CONN-2-BGP-VRFS
   !
   vrf VRF11
      rd 10.255.0.4:11
      route-target import evpn 11:11
      route-target export evpn 11:11
      router-id 10.255.0.4
      neighbor 10.255.1.96 peer group MLAG-IPv4-UNDERLAY-PEER
      neighbor 10.255.1.96 description DCX-LEAF1A_Vlan3010
      redistribute connected route-map RM-CONN-2-BGP-VRFS
   !
   vrf VRF12
      rd 10.255.0.4:12
      route-target import evpn 12:12
      route-target export evpn 12:12
      router-id 10.255.0.4
      neighbor 10.255.1.96 peer group MLAG-IPv4-UNDERLAY-PEER
      neighbor 10.255.1.96 description DCX-LEAF1A_Vlan3011
      redistribute connected route-map RM-CONN-2-BGP-VRFS
```

## BFD

### Router BFD

#### Router BFD Multihop Summary

| Interval | Minimum RX | Multiplier |
| -------- | ---------- | ---------- |
| 300 | 300 | 3 |

#### Router BFD Device Configuration

```eos
!
router bfd
   multihop interval 300 min-rx 300 multiplier 3
```

## Multicast

### IP IGMP Snooping

#### IP IGMP Snooping Summary

| IGMP Snooping | Fast Leave | Interface Restart Query | Proxy | Restart Query Interval | Robustness Variable |
| ------------- | ---------- | ----------------------- | ----- | ---------------------- | ------------------- |
| Enabled | - | - | - | - | - |

#### IP IGMP Snooping Device Configuration

```eos
```

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### PL-LOOPBACKS-EVPN-OVERLAY

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.255.0.0/27 eq 32 |
| 20 | permit 10.255.1.0/27 eq 32 |

##### PL-MLAG-PEER-VRFS

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.255.1.96/31 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.255.0.0/27 eq 32
   seq 20 permit 10.255.1.0/27 eq 32
!
ip prefix-list PL-MLAG-PEER-VRFS
   seq 10 permit 10.255.1.96/31
```

### Route-maps

#### Route-maps Summary

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | - | - | - |

##### RM-CONN-2-BGP-VRFS

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | deny | ip address prefix-list PL-MLAG-PEER-VRFS | - | - | - |
| 20 | permit | - | - | - | - |

##### RM-MLAG-PEER-IN

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | - | origin incomplete | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
!
route-map RM-CONN-2-BGP-VRFS deny 10
   match ip address prefix-list PL-MLAG-PEER-VRFS
!
route-map RM-CONN-2-BGP-VRFS permit 20
!
route-map RM-MLAG-PEER-IN permit 10
   description Make routes learned over MLAG Peer-link less preferred on spines to ensure optimal routing
   set origin incomplete
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| MGMT | disabled |
| VRF10 | enabled |
| VRF11 | enabled |
| VRF12 | enabled |

### VRF Instances Device Configuration

```eos
!
vrf instance MGMT
!
vrf instance VRF10
!
vrf instance VRF11
!
vrf instance VRF12
```

## Virtual Source NAT

### Virtual Source NAT Summary

| Source NAT VRF | Source NAT IPv4 Address | Source NAT IPv6 Address |
| -------------- | ----------------------- | ----------------------- |
| VRF10 | 10.255.10.4 | - |
| VRF11 | 10.255.11.4 | - |
| VRF12 | 10.255.12.4 | - |

### Virtual Source NAT Configuration

```eos
!
ip address virtual source-nat vrf VRF10 address 10.255.10.4
ip address virtual source-nat vrf VRF11 address 10.255.11.4
ip address virtual source-nat vrf VRF12 address 10.255.12.4
```

## EOS CLI Device Configuration

```eos
!
agent KernelFib environment KERNELFIB_PROGRAM_ALL_ECMP='true'
```
