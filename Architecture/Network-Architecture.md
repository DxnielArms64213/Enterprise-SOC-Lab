# Network Architecture

## Overview

The Enterprise SOC Homelab uses a VMware Host-Only network to create an isolated environment for the virtual machines.

The network allows the systems within the lab to communicate with each other while keeping the environment separate from the normal home network.

## Network Configuration

| Setting      | Configuration     |
| ------------ | ----------------- |
| Network      | VMware VMnet1     |
| Network Type | Host-Only         |
| Subnet       | `192.168.50.0/24` |
| Subnet Mask  | `255.255.255.0`   |
| DHCP         | Disabled          |

DHCP is disabled so that the lab systems can use predictable static IP addresses.

## IP Address Plan

| Device         | Role                        | IP Address      |
| -------------- | --------------------------- | --------------- |
| Windows Host   | VMware Host                 | `192.168.50.1`  |
| Kali Linux     | Security/Attack Workstation | `192.168.50.10` |
| Ubuntu Server  | Future Wazuh Server         | `192.168.50.20` |
| Windows Server | Domain Controller / DNS     | `192.168.50.30` |
| Windows Client | Domain Endpoint             | `192.168.50.40` |

## Network Diagram

```text
                         Windows Host
                         192.168.50.1
                              │
                              │
                       VMware VMnet1
                        Host-Only Network
                       192.168.50.0/24
                              │
              ┌───────────────┼────────────────┐
              │               │                │
              ▼               ▼                ▼
        Kali Linux      Ubuntu Server    Windows Server
       192.168.50.10    192.168.50.20    192.168.50.30
      Security/Attack   Future Wazuh       AD + DNS
       Workstation        Server          Controller
                                                  │
                                                  ▼
                                           Windows Client
                                           192.168.50.40
                                            Domain Endpoint
```

## Why Host-Only Networking Was Chosen

Host-Only networking provides an isolated environment for the SOC lab.

The virtual machines can communicate with each other and with the Windows host, while remaining separated from the normal home network.

This is important because the lab will eventually be used to generate and investigate suspicious activity.

## Why Static IP Addresses Are Used

Static IP addresses provide predictable addressing for important infrastructure.

For example:

* Windows Server provides Active Directory and DNS
* Ubuntu will provide Wazuh
* Kali provides the security testing workstation
* Windows Client acts as the monitored endpoint

Predictable addresses make it
