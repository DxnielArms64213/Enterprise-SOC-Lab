# Daily Log — 2026/09/17

## Objectives

* Complete the Windows Client setup for the SOC lab.
* Join the Windows Client to the Active Directory domain.
* Verify the security baseline Group Policy applies correctly.
* Install and configure Sysmon on the Windows Client.
* Verify Sysmon is generating security telemetry.
* Return the Windows Client to the isolated lab network.

## Work Completed

### Task 1 — Configure Windows Client Networking

**What I did:**

* Configured the Windows 11 client with the static IP address `192.168.50.40`.
* Configured the subnet mask as `255.255.255.0`.
* Configured the Domain Controller (`192.168.50.30`) as the preferred DNS server.
* Verified connectivity between the Windows Client and Domain Controller.

**Why I did it:**

* The Windows Client needs reliable communication with the Domain Controller for Active Directory authentication and Group Policy.
* Using the dedicated `192.168.50.0/24` VMnet1 Host-only network keeps the SOC environment isolated from the physical network.

### Task 2 — Join Windows Client to Active Directory

**What I did:**

* Renamed the Windows Client to `WIN-CLIENT`.
* Joined the machine to the `soclab.local` Active Directory domain.
* Logged into the domain using the `SOCLAB\labuser` account.
* Verified the domain authentication using:

```text
whoami
```

* The command returned:

```text
soclab\labuser
```

**Why I did it:**

* Joining the client to Active Directory allows it to operate as an enterprise endpoint within the lab.
* This provides a realistic environment for testing authentication, Group Policy, endpoint monitoring and security events.

### Task 3 — Resolve Local and Domain Account Confusion

**What I did:**

* Identified that `WIN-CLIENT\labuser` and `SOCLAB\labuser` are separate accounts.
* The first account is a local Windows account, while the second is the Active Directory domain account.
* Reset the Active Directory `labuser` password and successfully authenticated using the domain account.

**Why I did it:**

* Understanding the distinction between local and domain accounts is important when troubleshooting authentication in an Active Directory environment.
* The domain account is required for testing enterprise authentication and domain-based security policies.

### Task 4 — Apply the Security Baseline GPO

**What I did:**

* Moved the `WIN-CLIENT` computer object into the `Lab-Computers` OU.
* Forced Group Policy to update using:

```text
gpupdate /force
```

* Used `gpresult` to verify the applied policies.
* Confirmed that `Lab - Security Baseline` appeared under **Applied Group Policy Objects**.

**Why I did it:**

* The security baseline GPO contains the auditing configuration required to generate useful Windows security events.
* Applying the GPO to the client ensures that security-related activity can be logged and later collected by Wazuh.

### Task 5 — Install and Verify Sysmon

**What I did:**

* Downloaded Microsoft Sysmon for Windows.
* Installed the 64-bit version, `Sysmon64.exe`.
* Applied the SwiftOnSecurity Sysmon configuration as the initial monitoring baseline.
* Verified that the Sysmon service and event log were available.
* Opened:

```text
Applications and Services Logs
└── Microsoft
    └── Windows
        └── Sysmon
            └── Operational
```

* Confirmed that Sysmon was generating events.
* Used Event Viewer with administrative privileges to access the Sysmon Operational log.

**Why I did it:**

* Windows Event Logs provide security auditing, but Sysmon provides additional endpoint telemetry that is valuable for security monitoring.
* Sysmon can record information such as process creation, process relationships and network activity.
* This telemetry will later be collected by the Wazuh Agent and analysed by the Wazuh Server.

### Task 6 — Restore the Isolated Lab Network

**What I did:**

* Temporarily changed the Windows Client network adapter to NAT to download Sysmon.
* Changed the adapter back to VMware VMnet1 Host-only after installation.
* Restored the Windows Client's static network configuration:

  * IP: `192.168.50.40`
  * Subnet: `255.255.255.0`
  * Gateway: blank
  * DNS: `192.168.50.30`

**Why I did it:**

* Internet access was only required temporarily to obtain the software.
* Returning the client to VMnet1 maintains the intended isolation of the SOC lab.

## Current Status

* Windows 11 Client: Complete
* Hostname: `WIN-CLIENT`
* IP address: `192.168.50.40`
* Active Directory domain: `soclab.local`
* Domain authentication: Verified
* `Lab - Security Baseline` GPO: Applied
* Sysmon: Installed and generating events
* Network: Restored to isolated VMnet1 Host-only network
* Wazuh Server: Not yet installed
* Wazuh Agent: Not yet installed

## Next Steps

* Install and configure the Wazuh Server on Ubuntu (`192.168.50.20`).
* Install the Wazuh Agent on `WIN-CLIENT`.
* Configure Windows Event Log and Sysmon collection.
* Verify communication between the Wazuh Agent and Wazuh Server.
* Begin generating and investigating security alerts.

