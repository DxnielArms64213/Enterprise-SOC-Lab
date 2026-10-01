# Daily Log — 2026/09/12

## Objectives

* Resolve the remaining network connectivity issue
* Install and configure the Windows Server VM
* Configure Windows Server with a static IP
* Install Active Directory Domain Services
* Promote Windows Server to a Domain Controller
* Create the `soclab.local` Active Directory domain
* Configure and verify DNS
* Verify the Active Directory environment

## Work Completed

* Resolved the Ubuntu Server → Windows Host connectivity issue
* Confirmed connectivity across the lab network
* Installed Windows Server 2022
* Configured Windows Server with the static IP `192.168.50.30/24`
* Configured the Windows Server hostname as `DC01`
* Installed Active Directory Domain Services
* Promoted `DC01` to a Domain Controller
* Created the `soclab.local` Active Directory forest
* Configured DNS during the domain controller promotion
* Verified Active Directory Users and Computers
* Verified the `soclab.local` DNS zone

### Task 1 — Resolve the Network Connectivity Issue

**What I did:**

* Investigated why Ubuntu Server could not ping the Windows host
* Identified Windows Defender Firewall as the cause
* Enabled the ICMPv4 Echo Request firewall rule
* Retested connectivity from Ubuntu Server
* Confirmed successful communication with the Windows host
* Confirmed that Kali Linux could also communicate with the Windows host

**Why I did it:**

* Reliable network connectivity is required before adding additional systems to the lab
* The troubleshooting provided practical experience identifying whether connectivity problems were caused by networking or host firewall configuration

### Task 2 — Install Windows Server

**What I did:**

* Created a Windows Server 2022 virtual machine in VMware
* Configured the VM to use the VMnet1 Host-Only network
* Installed Windows Server 2022
* Configured the server with the static IP `192.168.50.30/24`

**Why I did it:**

* Windows Server will provide the core enterprise services required for the lab
* It will eventually act as the Domain Controller and provide Active Directory and DNS services

### Task 3 — Configure the Domain Controller

**What I did:**

* Renamed the Windows Server to `DC01`
* Installed Active Directory Domain Services
* Promoted `DC01` to a Domain Controller
* Created a new Active Directory forest using the domain `soclab.local`
* Configured the NetBIOS name as `SOCLAB`
* Enabled DNS during the domain controller promotion
* Completed the prerequisite checks successfully

**Why I did it:**

* Active Directory provides the simulated enterprise identity and management environment
* A Domain Controller allows the lab to simulate users, computers, authentication, groups and security policies

### Task 4 — Verify Active Directory and DNS

**What I did:**

* Opened Active Directory Users and Computers
* Confirmed that the `SOCLAB.LOCAL` domain was available
* Verified the default Active Directory containers
* Opened DNS Manager
* Confirmed that the `soclab.local` forward lookup zone existed

**Why I did it:**

* Verification confirmed that Active Directory and DNS had been successfully configured
* These services will be required when Windows Client systems are joined to the domain

## Current Network Configuration

| Device         | IP Address      | Role                          |
| -------------- | --------------- | ----------------------------- |
| Windows Host   | `192.168.50.1`  | VMware Host / VMnet1          |
| Kali Linux     | `192.168.50.10` | Security / Attack Workstation |
| Ubuntu Server  | `192.168.50.20` | Future Wazuh Server           |
| Windows Server | `192.168.50.30` | Domain Controller / DNS       |
| Windows Client | `192.168.50.40` | Future Enterprise Endpoint    |

Network: `192.168.50.0/24`

DHCP: Disabled

## Current Status

* Kali Linux installed
* Ubuntu Server installed
* VMware Host-Only network configured
* Ubuntu Server static IP configured: `192.168.50.20/24`
* Windows Host connectivity issue resolved
* Windows Server 2022 installed
* Windows Server static IP configured: `192.168.50.30/24`
* Windows Server renamed to `DC01`
* Active Directory Domain Services installed
* `DC01` promoted to Domain Controller
* `soclab.local` Active Directory forest created
* DNS configured
* Active Directory verified
* DNS zone verified
* Active Directory organisational structure not yet customised
* Domain users and security groups not yet configured
* Security baseline GPO not yet configured
* Windows Client not configured
* Sysmon not configured
* Wazuh not installed

## Next Steps

* Create the Active Directory organisational structure
* Create the required domain user accounts
* Create security groups
* Configure the security baseline Group Policy Object
* Configure Windows security auditing
* Create and configure the Windows Client
* Join the Windows Client to the `soclab.local` domain
