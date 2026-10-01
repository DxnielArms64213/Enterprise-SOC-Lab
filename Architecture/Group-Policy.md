# Group Policy — Security Baseline

## Overview

Group Policy is used to centrally configure security-related settings across computers within the Active Directory environment.

A dedicated security baseline Group Policy Object was created for the lab endpoints.

## Group Policy Object

```text
Name: Lab - Security Baseline
```

The GPO is linked to:

```text
Lab-Computers
```

This means that domain computers placed inside `Lab-Computers` can receive the security configuration.

## Configured Audit Policies

The following Advanced Audit Policy settings were configured:

* Audit Process Creation
* Audit Logon
* Audit Logoff
* Audit User Account Management
* Audit Security Group Management
* Audit Audit Policy Change

Successful and failed events were enabled where appropriate.

## Purpose of the Audit Policies

### Audit Process Creation

Provides visibility into processes being started on the system.

This can help when investigating suspicious programs, commands or scripts.

### Audit Logon

Provides visibility into successful and failed authentication attempts.

This can help identify suspicious authentication activity such as repeated failed logins.

### Audit Logoff

Provides visibility into users ending their sessions.

This provides additional context when investigating user activity.

### Audit User Account Management

Provides visibility into changes involving user accounts.

Examples include:

* User creation
* User deletion
* Account changes

These events can help identify suspicious account activity.

### Audit Security Group Management

Provides visibility into changes to security group membership.

This is useful for identifying potentially suspicious changes to access or privileges.

### Audit Audit Policy Change

Provides visibility into changes to the system's audit configuration.

This can be useful during investigations because an attacker may attempt to reduce security visibility by changing auditing settings.

## Why This Is Important to the SOC

The purpose of the security baseline is to generate useful security telemetry from the Windows environment.

The eventual telemetry pipeline will be:

```text
Windows Client
      │
      ├── Windows Security Events
      │
      └── Sysmon
             │
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
       SOC Investigation
```

The Group Policy therefore forms part of the telemetry layer of the SOC.

## Future Development

The security baseline will be expanded as the project develops.

Future components will include:

* Sysmon
* Wazuh Agent
* Additional Windows security configuration
* Wazuh detection rules
* Detection testing
* Incident-response exercises

The long-term goal is to create an endpoint that generates realistic security telemetry which can be monitored and investigated through the SOC environment.
