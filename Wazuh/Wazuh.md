# Wazuh

## Overview

Wazuh is the central security monitoring platform used by the Enterprise SOC Homelab.

It provides the infrastructure required to collect endpoint telemetry, analyse security events, generate alerts and provide a central interface for investigation.

The Wazuh deployment consists of:

* Wazuh Manager
* Wazuh Indexer
* Wazuh Dashboard
* Wazuh Agent

The Wazuh server is hosted on an Ubuntu Server VM at `192.168.50.20`.

## Wazuh Architecture

The Wazuh deployment follows this general telemetry flow:

```text
                         WIN-CLIENT
                             │
                    ┌────────▼────────┐
                    │  Wazuh Agent    │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ Wazuh Manager   │
                    │    .20          │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ Wazuh Indexer   │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ Wazuh Dashboard │
                    └────────┬────────┘
                             │
                             ▼
                       SOC Analyst
```

The Windows Client generates security telemetry through Windows logging and Sysmon.

The Wazuh Agent collects relevant telemetry and communicates with the Wazuh Manager.

The Manager processes the incoming data and applies Wazuh's analysis and detection capabilities.

The Indexer stores the resulting data, while the Dashboard provides the interface used to view and investigate events and alerts.

## Installation Environment

| Setting           | Value                   |
| ----------------- | ----------------------- |
| Hostname          | `wzuh`                  |
| Operating System  | Ubuntu Server 24.04     |
| IP Address        | `192.168.50.20`         |
| Network           | VMware VMnet1 Host-Only |
| Subnet            | `192.168.50.0/24`       |
| Primary Interface | `ens33`                 |
| DNS Server        | `192.168.50.30`         |
| Wazuh Version     | `4.14`                  |
| Deployment        | All-in-one              |

The Wazuh server uses the Ubuntu VM as a dedicated security monitoring system within the isolated SOC network.

## Network Configuration

The Wazuh server was configured with a static IP address:

```text
IP Address:  192.168.50.20/24
Interface:   ens33
DNS:         192.168.50.30
```

DHCP is not used on the primary SOC network.

The static address allows the other systems in the lab to reliably communicate with the Wazuh Manager.

The Wazuh server was connected to VMware VMnet1, providing access to the isolated `192.168.50.0/24` SOC network.

## Wazuh Installation

Wazuh was deployed using the official Wazuh all-in-one installation script.

The installation method deployed the three main Wazuh server components:

```text
Wazuh Indexer
      │
      ├── Stores security data
      │
Wazuh Manager
      │
      ├── Processes and analyses events
      │
Wazuh Dashboard
      │
      └── Provides the analyst interface
```

The all-in-one deployment was appropriate for the homelab because all components can run on a single dedicated Ubuntu server.

## Wazuh Services

The installation resulted in three primary Wazuh services being deployed.

### Wazuh Manager

The Wazuh Manager is responsible for receiving and processing information from Wazuh Agents.

It provides the central analysis and detection component of the deployment.

Responsibilities include:

* Receiving agent data
* Analysing security events
* Applying detection rules
* Generating alerts
* Managing connected agents

### Wazuh Indexer

The Wazuh Indexer provides the storage and search layer for Wazuh data.

It allows security events and alerts to be stored and queried by the Dashboard.

### Wazuh Dashboard

The Wazuh Dashboard provides the web-based interface used to interact with the Wazuh platform.

It allows the analyst to:

* View agents
* Search events
* Investigate alerts
* Review security data
* Monitor endpoint activity

The Dashboard is accessible from the SOC network at:

```text
https://192.168.50.20
```

## Service Verification

After installation, the Wazuh services were checked to confirm that the deployment had completed successfully.

The following components were verified as operational:

* Wazuh Manager
* Wazuh Indexer
* Wazuh Dashboard

All three services were reported as active after installation.

A Java/OpenSearch deprecation warning was also observed during installation. This did not prevent the Wazuh services from starting and was not treated as a deployment failure.

## Wazuh Dashboard

The Wazuh Dashboard provides the main interface for interacting with the SOC monitoring infrastructure.

The Dashboard was successfully accessed through the Wazuh server's static IP address.

Administrative credentials were configured during installation and stored separately from the project documentation.

Sensitive credentials are intentionally not stored in this repository.

## Agent Deployment

The first monitored endpoint added to Wazuh was `WIN-CLIENT`.

The agent was configured with:

| Setting          | Value           |
| ---------------- | --------------- |
| Agent Name       | `Win-Client`    |
| Operating System | Windows         |
| Wazuh Manager    | `192.168.50.20` |
| Endpoint IP      | `192.168.50.40` |

The Wazuh Agent was installed on the Windows Client and configured to communicate with the Wazuh Manager.

## Internet Connectivity During Agent Installation

The primary SOC network is intentionally isolated and does not provide internet access.

The Windows Client therefore temporarily used a second VMware NAT network adapter.

This provided the external connectivity required to download the Wazuh Agent package from the Wazuh package repository.

The NAT connection was separate from the primary `192.168.50.0/24` SOC network.

After the required software was downloaded and installed, the Wazuh Agent was configured to communicate with the Manager using the isolated SOC network.

## Agent Installation Verification

The Wazuh Agent service was started on `WIN-CLIENT` after installation.

The Windows service was verified as running:

```text
WazuhSvc
Status: Running
```

The Wazuh Dashboard subsequently displayed the `Win-Client` agent with an **Active** status.

This confirmed that:

1. The agent was installed successfully.
2. The agent was running on the Windows endpoint.
3. The endpoint could communicate with the Wazuh Manager.
4. The Manager successfully recognised the endpoint.

## Telemetry Pipeline

The current monitoring pipeline is:

```text
                    WIN-CLIENT
                         │
              ┌──────────┴──────────┐
              │                     │
         Windows Logs            Sysmon
              │                     │
              └──────────┬──────────┘
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
                         │
                         ▼
                    SOC Analyst
```

This creates the foundation for the SOC's detection and investigation workflow.

## Relationship With Sysmon

Sysmon provides detailed endpoint telemetry on `WIN-CLIENT`.

The Wazuh Agent acts as the collection and forwarding mechanism that allows this telemetry to reach the central Wazuh infrastructure.

This creates a separation of responsibilities:

```text
Sysmon
  │
  └── Generates endpoint telemetry

Wazuh Agent
  │
  └── Collects and forwards telemetry

Wazuh Manager
  │
  └── Processes and analyses telemetry

Wazuh Indexer
  │
  └── Stores telemetry and alerts

Wazuh Dashboard
  │
  └── Presents information to the analyst
```

This architecture allows the endpoint to remain relatively lightweight while the central Wazuh server performs the main monitoring functions.

## Current Status

The Wazuh monitoring infrastructure is operational.

Current state:

* Ubuntu Server 24.04 configured
* Static IP `192.168.50.20` configured
* Wazuh Indexer installed
* Wazuh Manager installed
* Wazuh Dashboard installed
* All Wazuh services verified as active
* Dashboard accessible
* `WIN-CLIENT` Wazuh Agent installed
* `WazuhSvc` running on `WIN-CLIENT`
* `Win-Client` agent showing as Active
* Sysmon generating endpoint telemetry

## Security Monitoring Workflow

The Wazuh deployment provides the foundation for the planned SOC workflow:

```text
Attack Simulation
       │
       ▼
WIN-CLIENT
       │
       ▼
Windows / Sysmon Telemetry
       │
       ▼
Wazuh Agent
       │
       ▼
Wazuh Manager
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
       │
       ▼
Incident Documentation
```

Kali will later be used to generate controlled security events against the lab environment.

The resulting activity will provide realistic telemetry that can be collected by Wazuh and investigated from the perspective of a SOC analyst.

## Future Configuration

The Wazuh deployment will be expanded as the SOC develops.

Planned areas include:

* Sysmon event collection
* Custom detection rules
* Windows security event monitoring
* MITRE ATT&CK mapping
* Alert tuning
* Log analysis
* IOC identification
* Incident investigation
* Automated response testing
* Controlled attack simulations

The goal is to progress from simply monitoring the Windows endpoint to building a complete detection, investigation and incident-response workflow.

