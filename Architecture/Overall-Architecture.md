# Overall SOC Architecture

## Overview

The Enterprise SOC Homelab is designed to simulate the basic workflow of a Security Operations Centre.

The environment contains systems that represent an enterprise endpoint, domain infrastructure, security testing workstation and central security monitoring platform.

The primary objective is to demonstrate the complete security workflow from suspicious activity through to detection, investigation and incident response.

## SOC Architecture

```text
                         Security Testing
                               │
                               ▼
                        ┌──────────────┐
                        │ Kali Linux   │
                        │ Attack/Test  │
                        │ Workstation  │
                        └──────┬───────┘
                               │
                       Simulated Activity
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Windows Client   │
                    │                     │
                    │ Windows Event Logs  │
                    │       +             │
                    │      Sysmon         │
                    │       +             │
                    │   Wazuh Agent       │
                    └──────────┬──────────┘
                               │
                         Security
                         Telemetry
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Ubuntu Server     │
                    │       Wazuh         │
                    │   Monitoring/SIEM   │
                    └──────────┬──────────┘
                               │
                           Detection
                               │
                               ▼
                            Alert
                               │
                               ▼
                           Triage
                               │
                               ▼
                        Investigation
                               │
                               ▼
                    MITRE ATT&CK Mapping
                               │
                               ▼
                      Incident Response
                               │
                               ▼
                    Documentation / Report
```

## Main Components

### Kali Linux

Kali Linux acts as the security testing workstation.

It will be used to generate controlled activity against systems inside the lab.

Examples include:

* Reconnaissance
* Network testing
* Controlled attack simulations
* Testing detection capabilities

The purpose is not simply to attack the systems, but to generate realistic security events that can then be detected and investigated.

### Windows Server

Windows Server provides the core Windows enterprise infrastructure.

It currently provides:

* Active Directory Domain Services
* Domain Controller functionality
* DNS
* Domain users
* Security groups
* Group Policy

This provides the foundation for the simulated enterprise environment.

### Windows Client

The Windows Client represents an employee workstation within the simulated organisation.

It will eventually contain:

* Windows Event Logging
* Sysmon
* Wazuh Agent
* Active Directory domain membership
* Security baseline configuration

This will be the primary endpoint monitored by the SOC.

### Ubuntu Server

Ubuntu Server will become the central Wazuh server.

It will eventually receive telemetry from the Windows Client through the Wazuh Agent.

Wazuh will provide the central monitoring and detection functionality of the lab.

## Security Workflow

The project is designed around the following workflow:

```text
Security Event
      ↓
Telemetry Generated
      ↓
Wazuh Receives Telemetry
      ↓
Detection
      ↓
Alert
      ↓
Triage
      ↓
Investigation
      ↓
Determine Scope
      ↓
Identify Indicators of Compromise
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

## Purpose of the Architecture

The goal is to demonstrate more than simply installing cybersecurity tools.

The lab will demonstrate how security professionals can:

* Generate security events
* Collect telemetry
* Detect suspicious behaviour
* Investigate alerts
* Identify indicators of compromise
* Map activity to MITRE ATT&CK
* Perform incident response
* Document security incidents

This creates a practical end-to-end SOC workflow.

## Relationship Between the Systems

The systems have different roles within the SOC:

| System         | Primary Role                            |
| -------------- | --------------------------------------- |
| Kali Linux     | Security testing and attack simulation  |
| Windows Server | Active Directory and DNS infrastructure |
| Windows Client | Monitored enterprise endpoint           |
| Ubuntu Server  | Central Wazuh monitoring server         |

The network architecture defines **how these systems communicate**, while this architecture defines **how they work together as a security operation**.
