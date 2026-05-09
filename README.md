# MplsL3Vpn

# MPLS L3VPN Project – ISP to Customer Connectivity

## Overview

This project demonstrates a complete MPLS L3VPN deployment simulating a real-world Internet Service Provider (ISP) architecture. The lab was designed to build end-to-end customer connectivity using MPLS, LDP, MP-BGP VPNv4, VRF, and OSPF.

The objective of this project was to understand how service providers transport customer traffic securely across a shared MPLS backbone while maintaining complete routing isolation between customers.

---

# Network Topology

```text
PC1 --- CE1 --- PE(R3) --- P(R1) --- P(R2) --- PE(R4) --- CE2 --- PC2
```

## Device Roles

| Device    | Role                       |
| --------- | -------------------------- |
| CE1 / CE2 | Customer Edge Routers      |
| CE3 / CE4 | Customer Edge Routers      |
| R3 / R4   | Provider Edge (PE) Routers |
| R1 / R2   | Provider Core (P) Routers  |
| PC1 / PC2 | Customer End Devices       |
| PC3 / PC4 | Customer End Devices       |

---

# Technologies Used

## Core Technologies

* MPLS (Multiprotocol Label Switching)
* LDP (Label Distribution Protocol)
* MP-BGP VPNv4
* VRF (Virtual Routing and Forwarding)
* OSPF Routing Protocol
* Label Switching
* MPLS L3VPN
* Penultimate Hop Popping (PHP)
* Route Redistribution

---

# Project Objectives

* Build an MPLS transport network
* Configure MPLS label switching
* Establish LDP neighbors
* Create VRF-based customer isolation
* Configure MP-BGP VPNv4 between PE routers
* Exchange customer routes across the MPLS cloud
* Achieve CE-to-CE communication through MPLS
* Understand ISP-level MPLS architecture

---

# IP Addressing Scheme

| Device  | Interface  | IP Address     |
| ------- | ---------- | -------------- |
| R1 Lo0  | Loopback0  | 1.1.1.1/32     |
| R2 lo0  | Loopback0  | 2.2.2.2/32     |
| R3 Lo0  | Loopback0  | 3.3.3.3/32     |
| R4 Lo0  | Loopback0  | 4.4.4.4/32     |
| R3-R1   | Core Link  | 10.0.0.0/30    |
| R1-R2   | Core Link  | 20.0.0.0/30    |
| R2-R4   | Core Link  | 30.0.0.0/30    |
| R3-CE1  | PE-CE Link | 192.168.1.0/30 |
| R4-CE1  | PE-CE Link | 192.168.2.0/30 |
| R3-CE2  | PE-CE Link | 192.168.5.1/30 |
| R2-CE2  | PE-CE Link | 192.168.6.1/30 |
| CE1 LAN | LAN        | 192.168.3.1/24 |
| CE2 LAN | LAN        | 192.168.4.1/24 |
| CE3 LAN | LAN        | 172.16.1.2/24  |
| CE4 LAN | LAN        | 172.16.2.1/24  |

---

# MPLS Architecture

## MPLS Core

The MPLS core consists of:

* P routers responsible only for label switching
* PE routers responsible for customer VPN services

The provider core does not learn customer routes directly.

---

# MPLS Packet Flow

Inside the MPLS cloud, packets are forwarded using labels.

```text
[Transport Label][VPN Label][IP Packet]
```

## Label Functions

| Label Type      | Purpose                                   |
| --------------- | ----------------------------------------- |
| Transport Label | Used for MPLS forwarding across P routers |
| VPN Label       | Identifies the customer VRF on PE routers |

---

# Routing Design

## Core Routing

* OSPF Area 0 used inside the MPLS provider core
* Loopback interfaces advertised for LDP and MP-BGP

## PE-CE Routing

* OSPF used between CE and PE routers
* Route redistribution configured between OSPF and BGP

---

# VRF Configuration

A dedicated VRF was created for customer isolation.

## VRF Details

| VRF Name | RD    | Route Target |
| -------- | ----- | ------------ |
| A        | 100:1 | 100:1        |
| B        | 101:1 | 101:1        |

---

# MP-BGP VPNv4

MP-BGP was configured between PE routers to exchange VPNv4 routes.

## Key Features

* VPNv4 address-family
* Extended community support
* Route-target import/export
* VPN route propagation

---

# Important MPLS Concepts Practiced

## Push Operation

Ingress PE router pushes labels onto packets.

## Swap Operation

P routers swap incoming labels with outgoing labels.

## Pop Operation

Penultimate hop router removes the transport label before forwarding to the egress PE.

---

# Verification Commands

## MPLS Verification

```bash
show mpls interfaces
show mpls ldp neighbor
show mpls forwarding-table
show mpls ldp bindings
```

## Routing Verification

```bash
show ip route
show ip route vrf A
show ip cef
```

## MP-BGP Verification

```bash
show bgp vpnv4 unicast all
show bgp vpnv4 unicast summary
```

## VRF Verification

```bash
show ip vrf
show ip vrf interfaces
```

---

# Troubleshooting Performed

During implementation, several real-world MPLS issues were identified and resolved.

## Issues Resolved

### VRF-Aware Ping Issue

Problem:

* Normal ping from PE to CE failed

Solution:

* Used VRF-aware ping:

```bash
ping vrf A x.x.x.x
```

---

### Missing Remote Customer Routes

Problem:

* CE routers could not reach remote customer networks

Root Cause:

* Missing redistribution from BGP into OSPF VRF

Solution:

```bash
router ospf 10 vrf A
 redistribute bgp 100 subnets
```

---

### MP-BGP Route Exchange

Problem:

* VPN routes not imported into VRF

Solution:

```bash
neighbor x.x.x.x send-community extended
```

---

# Final Verification

## Successful Tests

| Test                     | Status     |
| ------------------------ | ---------- |
| LDP Neighbor Formation   | Successful |
| MPLS Label Switching     | Successful |
| VPNv4 Route Exchange     | Successful |
| VRF Route Learning       | Successful |
| PE-CE Connectivity       | Successful |
| CE1 to CE2 Communication | Successful |
| MPLS Traceroute          | Successful |

---

# Sample Successful Ping

```text
PC3> ping 192.168.2.2
84 bytes from 192.168.2.2 icmp_seq=1 ttl=250 time=92 ms
```

This confirms successful end-to-end MPLS VPN communication.

---

# Learning Outcomes

This project provided hands-on experience with:

* Service Provider Networking
* MPLS Core Deployment
* VPN Service Architecture
* BGP VPNv4
* MPLS Label Operations
* VRF Isolation
* ISP Routing Design
* Enterprise WAN Connectivity
* Advanced Routing Troubleshooting

---

# Future Enhancements

Future upgrades planned for this lab:

* Multiple VRFs
* Route Reflectors
* MPLS Traffic Engineering
* Segment Routing
* EVPN VXLAN
* Inter-VRF Route Leaking
* Carrier Supporting Carrier
* QoS over MPLS

---

# Conclusion

This project successfully demonstrated a complete MPLS L3VPN deployment from ISP core to customer connectivity.

The implementation included:

* MPLS Transport Network
* LDP Label Distribution
* MP-BGP VPNv4
* VRF-based Routing Isolation
* OSPF PE-CE Integration
* End-to-End Customer Communication

The project closely simulates real-world service provider environments and significantly strengthened practical knowledge of MPLS and advanced networking technologies.

---

# Author

Ravi

Network Engineering & Service Provider Technologies
