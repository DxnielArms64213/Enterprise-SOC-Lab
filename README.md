# Enterprise SOC Homelab

## 1. Overview

The **Enterprise SOC Homelab** is a self-built cybersecurity laboratory designed to simulate a small enterprise environment and provide hands-on experience with security monitoring, detection, investigation and incident response.

The project uses multiple virtual machines to create an isolated enterprise network containing Windows infrastructure, an endpoint, a security testing workstation and a dedicated security monitoring server.

The lab is designed around a realistic security workflow:

```text
Security Activity
       ↓
Telemetry
       ↓
Detection
       ↓
Alert
       ↓
Triage
       ↓
Investigation
       ↓
MITRE ATT&CK Mapping
       ↓
Incident Response
       ↓
Documentation
```

The goal is to build and document the environment from the ground up rather than simply installing individual cybersecurity tools.

---

## 2. Objectives

The main objectives of the project are to:

* Build and manage an isolated enterprise-style virtual network
* Configure Windows Server and Active Directory
* Create and manage domain users, groups and organisational units
* Configure Windows security auditing through Group Policy
* Deploy and configure a Windows endpoint
* Implement Sysmon for additional endpoint telemetry
* Deploy Wazuh as the central security monitoring platform
* Generate controlled suspicious activity using Kali Linux
* Detect and investigate security events
* Practise security alert triage
* Identify indicators of compromise
* Map activity to the MITRE ATT&CK framework
* Practise incident-response procedures
* Document investigations and security incidents
* Develop practical SOC and cybersecurity skills

---

## 3. Lab Architecture

The lab uses an isolated VMware Host-Only network.

### Network

```text
VMware VMnet1
Host-Only Network
192.168.50.0/24
```

### Network Architecture

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

### SOC Architecture

The network infrastructure supports the larger SOC workflow:

```text
                         Kali Linux
                    Security/Attack Testing
                              │
                              ▼
                       Windows Client
                              │
                 ┌────────────┴────────────┐
                 │                         │
        Windows Event Logs              Sysmon
                 │                         │
                 └────────────┬────────────┘
                              ▼
                        Wazuh Agent
                              │
                              ▼
                     Ubuntu Wazuh Server
                              │
                              ▼
                       Detection / Alert
                              │
                              ▼
                           Triage
                              │
                              ▼
                        Investigation
                              │
                              ▼
                    Incident Response
                              │
                              ▼
                         Reporting
```

The network architecture describes **how the systems communicate**, while the SOC architecture describes **how security information moves through the environment**.

---

## 4. Components

### Kali Linux

**Role:** Security and attack workstation

Kali Linux will be used to perform controlled security testing against systems within the lab.

Planned uses include:

* Reconnaissance
* Network testing
* Controlled attack simulations
* Generating suspicious activity
* Testing detection capabilities

**IP:** `192.168.50.10`

---

### Windows Server

**Role:** Domain Controller and enterprise infrastructure

Windows Server provides the core Windows infrastructure for the lab.

Current services include:

* Active Directory Domain Services
* Domain Controller
* DNS
* Domain users
* Security groups
* Group Policy

**IP:** `192.168.50.30`

---

### Windows Client

**Role:** Simulated enterprise endpoint

The Windows Client will represent a normal endpoint within the simulated organisation.

It will eventually contain:

* Active Directory domain membership
* Windows security auditing
* Sysmon
* Wazuh Agent
* Security baseline configuration

**Planned IP:** `192.168.50.40`

---

### Ubuntu Server

**Role:** Central Wazuh security monitoring server

Ubuntu Server is dedicated to becoming the Wazuh server for the lab.

It will eventually provide:

* Centralised security monitoring
* Log collection
* Detection
* Alerting
* Endpoint visibility
* Security event analysis

**IP:** `192.168.50.20`

Wazuh has **not yet been installed**.

---

### Active Directory

Active Directory provides centralised identity and management for the Windows environment.

Current structure:

```text
Domain
│
├── Lab-Users
│   └── labuser
│
├── Lab-Computers
│
├── Lab-Servers
│
└── Lab-Groups
    └── SOC-Analysts
        └── labuser
```

---

### Group Policy

A security baseline GPO has been created:

```text
Lab - Security Baseline
```

It is linked to:

```text
Lab-Computers
```

The following auditing policies have been configured:

* Audit Process Creation
* Audit Logon
* Audit Logoff
* Audit User Account Management
* Audit Security Group Management
* Audit Audit Policy Change

These settings will provide useful security telemetry for later monitoring and detection.

---

## 5. Planned Features

The project will be developed in stages.

### Endpoint Monitoring

* Deploy Windows Client
* Join endpoint to Active Directory
* Apply security baseline
* Install Sysmon
* Configure Sysmon logging
* Install Wazuh Agent

### Security Monitoring

* Install Wazuh on Ubuntu
* Connect the Windows endpoint
* Collect Windows security events
* Collect Sysmon telemetry
* Configure detection rules
* Generate and investigate alerts

### Security Testing

Kali Linux will be used to generate controlled activity within the lab.

Testing will include activities designed to produce security telemetry that can be detected and investigated.

### Detection and Investigation

The project will eventually contain documented investigations covering:

* Alert triage
* Event analysis
* Process investigation
* Authentication activity
* Account and group changes
* Indicators of compromise
* MITRE ATT&CK techniques
* Scope determination
* Containment
* Remediation
* Recovery

### Incident Response

The lab will be used to practise a complete incident-response lifecycle:

```text
Alert
 ↓
Triage
 ↓
Investigation
 ↓
Determine Scope
 ↓
Identify IOCs
 ↓
MITRE ATT&CK Mapping
 ↓
Containment
 ↓
Remediation
 ↓
Recovery
 ↓
Incident Report
```

---

## 6. Documentation

The project is being documented throughout development.

Documentation includes:

* Daily build logs
* Network architecture
* Overall SOC architecture
* Active Directory structure
* Group Policy configuration
* Tool configuration
* Detection engineering
* Security investigations
* Incident reports
* Screenshots and supporting evidence

The documentation is intended to show not only the final result, but also the process used to build and troubleshoot the environment.

---

## 7. Current Progress

### Completed

* [x] Kali Linux installed
* [x] Ubuntu Server installed
* [x] VMware Host-Only network configured
* [x] Static IP addressing configured
* [x] Windows Server installed
* [x] Windows Server configured as Domain Controller
* [x] Active Directory configured
* [x] DNS configured
* [x] Active Directory organisational units created
* [x] Domain user created
* [x] `SOC-Analysts` security group created
* [x] User added to security group
* [x] Security baseline Group Policy created
* [x] Windows security auditing configured

### In Progress

* [ ] Windows Client VM
* [ ] Domain joining Windows Client
* [ ] Sysmon deployment
* [ ] Wazuh installation
* [ ] Wazuh Agent deployment

### Planned

* [ ] Detection engineering
* [ ] Controlled attack simulations
* [ ] Alert investigation
* [ ] MITRE ATT&CK mapping
* [ ] Incident-response exercises
* [ ] Incident reports

---

## 8. Security & Ethics

This laboratory is intended for **educational and authorised security testing only**.

All security testing will be performed against systems owned and controlled by the lab environment.

The lab uses an isolated Host-Only network to reduce the risk of accidentally affecting external systems.

No testing will intentionally target systems, networks or accounts without authorisation.

The purpose of the project is to develop practical defensive cybersecurity skills, including:

* Monitoring
* Detection
* Investigation
* Incident response
* Security analysis

---

## 9. Skills Demonstrated

This project is designed to demonstrate practical experience across multiple areas of cybersecurity and IT.

### Networking

* IPv4 addressing
* Subnetting
* Static IP configuration
* VMware virtual networking
* Network isolation
* Connectivity troubleshooting

### Windows Administration

* Windows Server
* Active Directory
* Domain Controllers
* DNS
* Users and groups
* Group Policy
* Windows security auditing

### Cybersecurity

* Security monitoring
* Log analysis
* Detection
* Alert triage
* Endpoint telemetry
* Incident investigation
* Incident response
* Indicators of compromise
* MITRE ATT&CK

### Security Tools

* Kali Linux
* Sysmon
* Wazuh
* Windows Event Logging
* VMware

### Professional Skills

* Technical documentation
* Troubleshooting
* System administration
* Security analysis
* Incident reporting
* Practical problem solving

---

## Project Status

**Status:** Active Development

The core Windows Server and Active Directory infrastructure has been established.

The next major stage is deploying the Windows Client, followed by endpoint telemetry through Sysmon and centralised monitoring through Wazuh.
