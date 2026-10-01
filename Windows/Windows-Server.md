# Windows Server

## Overview

Windows Server 2022 is used as the domain controller for the Enterprise SOC Homelab.

The server is named `DC01` and provides the central identity and directory services required by the lab's Windows environment.

The server hosts:

* Active Directory Domain Services
* DNS
* Group Policy
* Domain authentication
* Users and groups
* Computer management

## System Configuration

| Setting          | Value                        |
| ---------------- | ---------------------------- |
| Hostname         | `DC01`                       |
| Operating System | Windows Server 2022          |
| IP Address       | `192.168.50.30`              |
| Subnet Mask      | `255.255.255.0`              |
| Domain           | `soclab.local`               |
| Role             | Domain Controller            |
| DNS              | Local / Active Directory DNS |

The server uses a static IP address so that services such as Active Directory and DNS remain available at a predictable location.

## Active Directory

Active Directory Domain Services (AD DS) was installed and the server was promoted to a Domain Controller.

The lab uses the following domain:

```text
soclab.local
```

The domain provides a centralised identity and authentication system for the Windows environment.

Instead of managing accounts independently on each Windows machine, users and computers can be managed centrally through Active Directory.

## Domain Controller

`DC01` acts as the primary Domain Controller for the lab.

Its responsibilities include:

* User authentication
* Computer authentication
* Directory management
* Group management
* Group Policy
* DNS integration

The Windows Client communicates with DC01 when authenticating users and accessing domain resources.

## Organisational Units

The following Organisational Units (OUs) were created:

```text
soclab.local
│
├── Lab-Users
├── Lab-Computers
├── Lab-Servers
└── Lab-Groups
```

The OUs provide logical separation between different types of Active Directory objects.

### Lab-Users

Contains user accounts used within the lab.

The primary test account created was:

```text
labuser
```

### Lab-Computers

Contains domain-joined Windows computers that are managed through the lab's computer policies.

`WIN-CLIENT` was moved into this OU after joining the domain.

### Lab-Servers

Provides a dedicated location for server computer objects.

### Lab-Groups

Contains security groups used to organise permissions and roles.

The following group was created:

```text
SOC-Analysts
```

`labuser` was added to this group.

## Users and Groups

The lab uses Active Directory security groups to represent organisational roles.

The current structure includes:

```text
SOC-Analysts
└── labuser
```

Using groups instead of assigning permissions individually provides a more realistic enterprise-style approach to access management.

It also allows additional users to be added to the same role without redesigning permissions.

## DNS

DC01 provides DNS for the `soclab.local` domain.

The Windows Client uses:

```text
DNS Server: 192.168.50.30
```

Active Directory relies heavily on DNS for locating domain services.

The DNS configuration therefore allows the Windows Client to locate the Domain Controller and other domain resources.

## Group Policy

A Group Policy Object named:

```text
Lab - Security Baseline
```

was created and linked to the `Lab-Computers` OU.

The purpose of the policy is to establish security-related settings on domain-joined computers.

The policy is used to configure Windows auditing required by the SOC.

## Advanced Audit Policy

The following audit categories were enabled as part of the security baseline:

| Audit Category            | Configuration     |
| ------------------------- | ----------------- |
| Process Creation          | Success           |
| Logon                     | Success + Failure |
| Logoff                    | Enabled           |
| User Account Management   | Success + Failure |
| Security Group Management | Success + Failure |
| Audit Policy Change       | Success + Failure |

These settings provide useful Windows Security event telemetry for the SOC.

For example, successful and failed logons can help identify authentication activity, while process creation provides visibility into executable activity.

## Group Policy Verification

The applied Group Policy configuration was verified on the Windows Client.

The following command was used:

```cmd
gpresult /scope computer /r
```

This confirmed that:

```text
Lab - Security Baseline
```

was being applied to the computer.

This verification is important because creating a GPO does not by itself prove that the target computer is receiving the policy.

## Domain Authentication

The domain environment was tested using the `labuser` account.

The Windows Client successfully authenticated against the:

```text
soclab.local
```

domain.

The authenticated user context was verified using:

```cmd
whoami
```

which returned:

```text
soclab\labuser
```

This confirmed that the Windows Client was successfully joined to the domain and that domain authentication was functioning.

## Security Telemetry

DC01 provides an important source of Windows security telemetry for the SOC.

The configured audit policies generate events related to activities such as:

* User logons
* Failed authentication attempts
* Account management
* Group membership changes
* Process creation
* Security policy changes

These events can later be collected and analysed by Wazuh.

The resulting telemetry can be correlated with Sysmon events generated on `WIN-CLIENT`.

## Role in the SOC

DC01 represents the central identity and directory infrastructure of the simulated enterprise.

Its role is important because many real-world security incidents involve identity infrastructure, including:

* Compromised accounts
* Brute-force attempts
* Privilege escalation
* Group membership changes
* Suspicious authentication
* Lateral movement

By operating a real Active Directory environment, the lab can generate realistic authentication and directory-related telemetry for investigation.

## Current Status

The Windows Server environment is operational.

Current state:

* Windows Server 2022 installed
* Hostname configured as `DC01`
* Static IP `192.168.50.30` configured
* Active Directory Domain Services installed
* Domain Controller promotion completed
* `soclab.local` domain operational
* DNS configured
* Lab OUs created
* `labuser` created
* `SOC-Analysts` group created
* `labuser` added to `SOC-Analysts`
* `Lab - Security Baseline` GPO created
* Advanced Audit Policy configured
* GPO application verified

## Future Use

DC01 will continue to act as the identity and directory foundation of the SOC lab.

Future testing will use the domain environment to generate realistic security events, including controlled authentication attacks, account changes and lateral-movement scenarios.

These events will be collected by Wazuh and used to develop detection and investigation capabilities.

