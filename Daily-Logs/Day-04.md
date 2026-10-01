# Daily Log — 2026/09/13

## Objectives

* Configure the Active Directory organisational structure
* Create the required domain user account
* Create a security group for SOC users
* Configure a security baseline Group Policy Object
* Configure Windows security auditing
* Prepare the environment for the future Windows Client

## Work Completed

* Created custom Active Directory organisational units
* Created the `labuser` domain account
* Placed `labuser` in the `Lab-Users` organisational unit
* Created the `SOC-Analysts` security group
* Added `labuser` to the `SOC-Analysts` group
* Created the `Lab - Security Baseline` Group Policy Object
* Linked the GPO to `Lab-Computers`
* Configured Advanced Audit Policy settings
* Verified the Group Policy configuration

### Task 1 — Create the Active Directory Organisational Structure

**What I did:**

* Created the following organisational units:

  * `Lab-Users`
  * `Lab-Computers`
  * `Lab-Servers`
  * `Lab-Groups`

**Why I did it:**

* The default Active Directory containers are not sufficient for representing the structure of the simulated enterprise environment
* Custom organisational units provide a logical structure for managing users, computers, servers and security groups
* The structure will also allow security policies to be applied to specific types of systems

### Task 2 — Create the Domain User

**What I did:**

* Created the domain user `labuser`
* Placed the account inside the `Lab-Users` organisational unit

**Why I did it:**

* The lab requires realistic domain accounts for authentication and security testing
* The account will later be used when testing authentication events, account activity and security monitoring

### Task 3 — Create the SOC Security Group

**What I did:**

* Created the `SOC-Analysts` group inside `Lab-Groups`
* Configured the group as a Global Security group
* Added `labuser` as a member

**Why I did it:**

* Security groups provide a way to manage permissions and access based on roles
* The `SOC-Analysts` group represents a security operations role within the simulated enterprise environment

### Task 4 — Create the Security Baseline GPO

**What I did:**

* Created the `Lab - Security Baseline` Group Policy Object
* Linked the GPO to the `Lab-Computers` organisational unit
* Configured security auditing policies

The following audit policies were configured:

* Audit Process Creation — Success
* Audit Logon — Success and Failure
* Audit Logoff
* Audit User Account Management — Success and Failure
* Audit Security Group Management — Success and Failure
* Audit Audit Policy Change — Success and Failure

**Why I did it:**

* Security auditing generates useful telemetry for monitoring and investigation
* Process creation events can help identify suspicious program execution
* Logon and logoff events provide authentication and session information
* User and security group auditing can identify account and privilege changes
* Audit Policy Change events can identify modifications to the auditing configuration
* These events will later provide useful data for the Wazuh monitoring environment

### Task 5 — Verify the Group Policy Configuration

**What I did:**

* Verified that the `Lab - Security Baseline` GPO was linked to `Lab-Computers`
* Confirmed that the required auditing policies were configured

**Why I did it:**

* Verification ensures that the security baseline is correctly configured before Windows Client systems are added to the environment
* The Windows Client will later inherit these policies when it is placed in the `Lab-Computers` organisational unit

## Current Active Directory Structure

```text
SOCLAB.LOCAL
├── Lab-Users
│   └── labuser
├── Lab-Computers
├── Lab-Servers
└── Lab-Groups
    └── SOC-Analysts
        └── labuser
```

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
* Windows Server 2022 installed
* Windows Server static IP configured: `192.168.50.30/24`
* `DC01` configured as Domain Controller
* `soclab.local` Active Directory domain configured
* DNS configured
* Custom Active Directory organisational units created
* `labuser` domain account created
* `SOC-Analysts` security group created
* `labuser` added to `SOC-Analysts`
* `Lab - Security Baseline` GPO created
* Security auditing configured
* GPO linked to `Lab-Computers`
* Windows Client not yet configured
* Sysmon not yet configured
* Wazuh not yet installed
* Detection rules not yet configured
* Incident response testing not yet started

## Next Steps

* Create the Windows Client VM
* Configure the Windows Client with `192.168.50.40/24`
* Configure `192.168.50.30` as the client's DNS server
* Join the Windows Client to the `soclab.local` domain
* Move the computer account into `Lab-Computers`
* Verify the security baseline GPO is applied
* Install and configure Sysmon
* Install the Wazuh Agent
* Prepare Ubuntu Server for the Wazuh deployment
