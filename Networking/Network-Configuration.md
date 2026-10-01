# Network Configuration

## Overview

The Enterprise SOC Homelab uses a VMware Host-Only network to provide an isolated internal network for the lab systems.

The primary SOC network uses the `192.168.50.0/24` subnet. DHCP is disabled, so the lab systems use manually configured static IP addresses.

The Windows Client also has a temporary VMware NAT connection for internet access when required, such as downloading software and updates. This NAT connection is separate from the primary SOC network.

## Network Topology

```text
                         Physical Windows Host
                                  │
                           VMware Workstation
                                  │
                    ┌──────────────────────────┐
                    │          VMnet1          │
                    │        Host-Only         │
                    │     192.168.50.0/24      │
                    │      DHCP Disabled       │
                    └──────────────────────────┘
                       │        │        │        │
                       │        │        │        │
                    ┌──┴──┐  ┌──┴───┐  ┌──┴───┐  ┌──┴────────┐
                    │Kali │  │Wazuh │  │ DC01 │  │WIN-CLIENT │
                    │ .10 │  │ .20  │  │ .30  │  │   .40     │
                    └─────┘  └──────┘  └──────┘  └─────┬─────┘
                                                        │
                                                      NIC 2
                                                        │
                                                   ┌────▼─────┐
                                                   │ VMware NAT│
                                                   └────┬─────┘
                                                        │
                                                        ▼
                                                     Internet
```

All four virtual machines are independently connected to VMnet1. VMnet1 provides the shared internal network; the systems are not connected to each other in a chain.

The Windows Client has a second network interface connected to VMware NAT for temporary internet access.

## Network Configuration

| System       | IP Address      | Subnet Mask     | Primary Role           |
| ------------ | --------------- | --------------- | ---------------------- |
| Windows Host | `192.168.50.1`  | `255.255.255.0` | VMware Host Adapter    |
| Kali         | `192.168.50.10` | `255.255.255.0` | Attack Simulation      |
| Wazuh        | `192.168.50.20` | `255.255.255.0` | Security Monitoring    |
| DC01         | `192.168.50.30` | `255.255.255.0` | Active Directory / DNS |
| WIN-CLIENT   | `192.168.50.40` | `255.255.255.0` | Windows Endpoint       |

### Primary SOC Network

```text
Network:       192.168.50.0/24
Subnet Mask:   255.255.255.0
DHCP:          Disabled
```

## VMnet1 — Host-Only Network

VMnet1 is configured as a VMware Host-Only network.

* **Network:** `192.168.50.0/24`
* **Host Adapter:** `192.168.50.1`
* **DHCP:** Disabled

Host-Only networking allows the virtual machines to communicate with each other and with the physical Windows host without providing direct internet access through VMnet1.

DHCP is disabled because the lab uses static IP addresses. Predictable addressing is useful for infrastructure services such as Active Directory, DNS and Wazuh, as well as for troubleshooting and documentation.

## Static IP Addressing

Static addressing is used throughout the primary SOC network.

This provides predictable addressing for infrastructure services and makes the environment easier to document, troubleshoot and monitor.

Key infrastructure addresses include:

* Wazuh — `192.168.50.20`
* DC01 — `192.168.50.30`
* WIN-CLIENT — `192.168.50.40`

This is particularly important because the Windows Client needs to communicate with the Wazuh Manager, while the domain environment relies on DC01 having a predictable address for DNS and Active Directory services.

## DNS

DC01 provides DNS services for the `soclab.local` domain.

The Windows Client uses:

```text
DNS Server: 192.168.50.30
```

This allows the client to resolve internal domain resources and communicate with the Active Directory environment.

The Wazuh server is also configured to use DC01 as its DNS server on the SOC network.

## Default Gateway

The primary VMnet1 network does not have an internet gateway.

This is intentional because the SOC network is designed to remain isolated from the internet.

The Windows Client can temporarily use its separate VMware NAT adapter when external connectivity is required.

## Temporary NAT Connectivity

The Windows Client has a second network adapter connected to VMware NAT.

This was added because the isolated VMnet1 network does not provide internet access. The NAT connection provides:

* Internet connectivity
* A default gateway
* External DNS resolution

It was required to download the Wazuh Agent package from the Wazuh package repository.

The NAT adapter is **not part of the `192.168.50.0/24` SOC network**.

```text
WIN-CLIENT
    │
    ├── NIC 1 → VMnet1
    │            192.168.50.40
    │            SOC Network
    │
    └── NIC 2 → VMware NAT
                 Internet Access
```

The NAT adapter is considered temporary and can be removed once external connectivity is no longer required.

## Connectivity and Services

### DC01

DC01 provides:

* Active Directory Domain Services
* DNS
* Domain authentication
* Group Policy

### Wazuh

The Wazuh server provides the central security monitoring infrastructure:

* Wazuh Manager
* Wazuh Indexer
* Wazuh Dashboard

The Windows Client's Wazuh Agent communicates with the Wazuh Manager over the SOC network.

### WIN-CLIENT

WIN-CLIENT is the monitored Windows endpoint.

It communicates with:

* DC01 for domain services and DNS
* Wazuh for security monitoring
* Kali during controlled attack simulations

### Kali

Kali is used as the attack and security-testing workstation and communicates with systems on the isolated SOC network during controlled testing.

## Connectivity Verification

Connectivity was tested during deployment.

The Wazuh server successfully communicated with DC01 and the VMware host adapter.

The Windows Client successfully communicated with:

* DC01
* Wazuh
* The `soclab.local` domain
* The internet through the temporary NAT adapter

The Wazuh Agent successfully communicated with the Wazuh Manager at:

```text
192.168.50.20
```

The Wazuh Dashboard confirmed the `Win-Client` agent as **Active**.

## Design Rationale

The network was designed around three main requirements:

1. **Isolation** — the primary SOC network does not require direct internet connectivity.
2. **Predictability** — static IP addresses make infrastructure services and monitoring easier to configure and troubleshoot.
3. **Controlled connectivity** — internet access can be provided temporarily through a separate NAT adapter when required.

This provides a controlled environment in which attacks can be simulated, endpoint telemetry can be collected and security events can be investigated without making the core SOC network dependent on internet connectivity.

