# Windows Client

## Overview

`WIN-CLIENT` is the primary Windows endpoint in the Enterprise SOC Homelab.

It represents a simulated employee workstation within the `soclab.local` domain and acts as the main endpoint for security monitoring and controlled attack simulations.

The endpoint currently uses:

* Windows Security logging
* Sysmon
* Wazuh Agent

## System Configuration

| Setting            | Value                |
| ------------------ | -------------------- |
| Hostname           | `WIN-CLIENT`         |
| Operating System   | Windows 11 Pro       |
| Domain             | `soclab.local`       |
| VMnet1 IP          | `192.168.50.40`      |
| VMnet1 Subnet Mask | `255.255.255.0`      |
| VMnet1 Gateway     | None                 |
| DNS Server         | `192.168.50.30`      |
| Primary Network    | VMware VMnet1        |
| Monitoring         | Sysmon + Wazuh Agent |

The VMnet1 interface uses a static IP address on the isolated SOC network.

The Windows Client also has a separate VMware NAT interface for temporary internet access.

## Network Configuration

The primary network interface is connected to VMware VMnet1.

```text
Interface:     VMnet1
IP Address:    192.168.50.40
Subnet Mask:   255.255.255.0
Gateway:       None
DNS:           192.168.50.30
```

The VMnet1 interface does not use an internet gateway. This is intentional because the `192.168.50.0/24` network is designed to remain isolated from the internet.

DC01 provides DNS for the internal `soclab.local` domain.

## Temporary NAT Connectivity

A second network adapter is connected to VMware NAT to provide internet access when required.

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

The NAT interface is separate from the primary SOC network.

It was used to provide external connectivity required for tasks such as downloading the Wazuh Agent package.

The NAT connection is considered temporary and can be removed when external connectivity is no longer required.

## Active Directory Domain Join

`WIN-CLIENT` was successfully joined to the:

```text
soclab.local
```

domain.

The Domain Controller is:

```text
DC01
192.168.50.30
```

After joining the domain, the computer object was moved into:

```text
Lab-Computers
```

This allows the endpoint to receive the Group Policy assigned to domain computers.

## Domain Authentication

Domain authentication was verified using the `labuser` account.

The authenticated user context was checked with:

```cmd
whoami
```

The command returned:

```text
soclab\labuser
```

This confirmed that the Windows Client was successfully authenticating against the `soclab.local` domain.

## Group Policy

The endpoint receives the:

```text
Lab - Security Baseline
```

Group Policy Object from the `Lab-Computers` OU.

The applied policy was verified using:

```cmd
gpresult /scope computer /r
```

The output confirmed that the security baseline was being applied to the computer.

The policy provides the Windows audit configuration required to generate useful security telemetry.

## Windows Security Logging

The endpoint generates Windows Security events according to the audit policy configured through Group Policy.

The configured audit categories include:

* Successful and failed logons
* Logoff activity
* User account management
* Security group management
* Process creation
* Audit policy changes

These events provide authentication and security context for future monitoring and investigation.

## Sysmon

Sysmon was installed on `WIN-CLIENT` to provide additional endpoint telemetry.

The endpoint uses the SwiftOnSecurity Sysmon configuration.

Sysmon has been verified as generating events in the Windows Sysmon Operational event log.

Relevant telemetry includes activities such as:

* Process creation
* Network connections
* File activity
* Registry activity
* Process termination
* Image and driver loading

Sysmon provides additional endpoint visibility alongside the standard Windows Security logs.

## Wazuh Agent

The Wazuh Agent was installed on `WIN-CLIENT` and configured to communicate with the Wazuh Manager at:

```text
192.168.50.20
```

The Windows service used by the agent is:

```text
WazuhSvc
```

The service was verified as running after installation.

## Wazuh Agent Status

The Wazuh Agent was successfully installed and started on the Windows Client.

The Wazuh Dashboard subsequently displayed:

```text
Agent Name: Win-Client
Status:     Active
```

This confirms that the Wazuh Manager is currently communicating with the endpoint agent.

The agent's **Active** status confirms connectivity between the endpoint and Wazuh Manager. It does not by itself mean that every available Windows or Sysmon event has been configured for collection.

Additional Wazuh configuration and telemetry verification will be performed as the SOC monitoring environment is developed.

## Endpoint Telemetry Architecture

The endpoint currently provides multiple sources of security telemetry.

```text
                    WIN-CLIENT
                         │
          ┌──────────────┴──────────────┐
          │                             │
          ▼                             ▼
 Windows Security                    Sysmon
     Events                         Telemetry
          │                             │
          └──────────────┬──────────────┘
                         │
                         ▼
                   Wazuh Agent
                         │
                         ▼
                  Wazuh Manager
                         │
                         ▼
                  Wazuh Indexer
                         │
                         ▼
                 Wazuh Dashboard
```

The architecture provides the foundation for correlating Windows security events with Sysmon telemetry once the relevant Wazuh collection and detection configuration has been completed.

## Role in the SOC

`WIN-CLIENT` is the primary monitored endpoint in the lab.

It provides the environment in which normal and controlled security activity can be generated and observed.

The endpoint will be used for:

* Normal user activity
* Authentication testing
* Process monitoring
* Network activity monitoring
* Controlled attack simulations
* Detection testing
* Incident investigation

Kali will later be used to perform controlled security testing against the lab environment.

The resulting activity can generate telemetry on `WIN-CLIENT`, which will then be collected and analysed through the Wazuh infrastructure.

## Current Status

The Windows Client is operational and integrated into the domain and Wazuh environment.

Current state:

* Windows 11 Pro installed
* Hostname configured as `WIN-CLIENT`
* VMnet1 interface configured as `192.168.50.40/24`
* No gateway configured on the VMnet1 interface
* DC01 configured as the DNS server
* Joined to `soclab.local`
* Computer object moved to `Lab-Computers`
* Domain authentication verified
* `Lab - Security Baseline` GPO applied
* Windows audit policy configured
* Sysmon installed
* SwiftOnSecurity Sysmon configuration deployed
* Sysmon events verified
* Wazuh Agent installed
* `WazuhSvc` verified as running
* `Win-Client` shown as **Active** in the Wazuh Dashboard
* Separate VMware NAT interface available for temporary internet access

## Future Use

`WIN-CLIENT` will be the primary endpoint used for detection engineering and incident-response exercises.

Future scenarios will generate controlled security events and allow the SOC workflow to be tested:

```text
Attack Simulation
       │
       ▼
WIN-CLIENT
       │
       ▼
Endpoint Telemetry
       │
       ▼
Wazuh Collection
       │
       ▼
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
Incident Response
```

This provides the foundation for moving from infrastructure deployment into actual SOC operations.
