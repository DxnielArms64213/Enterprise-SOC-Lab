# Daily Log — 2026/09/22

## Objectives

* Complete Wazuh integration with the Windows Client.
* Configure the Windows Client to communicate with the Wazuh server.
* Deploy and activate the Wazuh Agent.
* Verify the Wazuh Manager, Indexer and Dashboard services.
* Understand the complete SOC architecture and telemetry flow.

## Work Completed

### Task 1 — Configured Temporary Internet Access for WIN-CLIENT

**What I did:**

* Added a second VMware network adapter to WIN-CLIENT.
* Kept the existing adapter connected to `VMnet1` for the isolated SOC network.
* Configured the second adapter to use VMware NAT.
* WIN-CLIENT received:

  * IPv4: `192.168.75.136`
  * Default Gateway: `192.168.75.2`
  * DNS: `192.168.75.2`

**Why I did it:**

* The SOC network uses a Host-Only network with no internet route.
* The Wazuh Agent installer needed to be downloaded from Wazuh's package repository.
* A temporary NAT adapter provided internet access without changing the main SOC network.

### Task 2 — Troubleshot External DNS

**What I did:**

* Tested internet connectivity using `Test-Connection`.
* Confirmed the NAT gateway was reachable.
* Tested DNS resolution for `packages.wazuh.com`.
* Used an external DNS server to verify that the hostname could be resolved.
* Confirmed that an IPv4 DNS record for `packages.wazuh.com` could be retrieved.

**Why I did it:**

* Internet connectivity and DNS resolution are separate networking functions.
* The Windows Client initially could communicate with the Wazuh server but could not resolve the external Wazuh package hostname.
* Testing the layers separately helped identify that the problem was related to DNS rather than the underlying network connection.

### Task 3 — Downloaded and Installed the Wazuh Agent

**What I did:**

* Downloaded the Wazuh Windows Agent installer.
* Verified that the downloaded installer existed and had a non-zero file size.
* Installed the Wazuh Agent using the Wazuh Manager address:

  * `192.168.50.20`
* Configured the agent name as:

  * `Win-Client`
* Verified that the `WazuhSvc` service was installed.
* Started the Wazuh Agent service.
* Confirmed the service status changed to `Running`.

**Why I did it:**

* The Wazuh Agent provides the connection between the monitored Windows endpoint and the central Wazuh infrastructure.
* Configuring the Manager address allows WIN-CLIENT to send security telemetry to the Wazuh server.

### Task 4 — Verified the Wazuh Server

**What I did:**

Verified the three main Wazuh services on Ubuntu:

* Wazuh Manager — active
* Wazuh Indexer — active
* Wazuh Dashboard — active

The Wazuh Indexer displayed a `SecurityManager` deprecation warning related to OpenSearch, but the service was confirmed to be running normally.

**Why I did it:**

* The Wazuh platform depends on these three components working together.
* The Manager processes agent data, the Indexer stores and indexes security data, and the Dashboard provides the analyst interface.

### Task 5 — Verified Wazuh Agent Connectivity

**What I did:**

* Opened the Wazuh Dashboard.
* Located `Win-Client` in the Agents section.
* Confirmed the agent status was:

  * **Active**

**Why I did it:**

* An active agent confirms that the Windows endpoint has successfully registered with the Wazuh Manager and is communicating with it.
* This established the first complete endpoint-to-Wazuh communication path in the lab.

### Task 6 — Reviewed the SOC Architecture

**What I did:**

Reviewed the purpose and relationship of the main components:

```text
Kali
  ↓
Windows Client
  ↓
Windows Events / Sysmon
  ↓
Wazuh Agent
  ↓
Wazuh Manager
  ↓
Wazuh Indexer
  ↓
Wazuh Dashboard
  ↓
SOC Analyst
```

I also reviewed the role of DC01 as the Active Directory and DNS server for the `soclab.local` domain.

**Why I did it:**

* Understanding how the components interact is important before beginning detection engineering and incident investigation.
* The objective is to understand the complete telemetry pipeline rather than simply installing security tools.

### Task 7 — Tested Domain Authentication

**What I did:**

* Logged into WIN-CLIENT using the domain account `SOCLAB\labuser`.

**Why I did it:**

* This confirmed that the Windows Client remains integrated with the `soclab.local` Active Directory environment.
* The login also provides a legitimate Windows authentication event that can later be used to verify that endpoint telemetry is reaching Wazuh.

## Current Status

* Windows Server / DC01 — operational
* Active Directory — operational
* DNS — operational
* Windows Client — operational
* Sysmon — operational
* Wazuh Manager — active
* Wazuh Indexer — active
* Wazuh Dashboard — active
* Wazuh Agent — installed and running
* `Win-Client` Wazuh agent — **Active**
* Temporary NAT adapter — currently enabled on WIN-CLIENT
* SOC network — `192.168.50.0/24`

## Architecture

```text
                    SOC NETWORK
                  192.168.50.0/24

Kali                 Wazuh                 DC01
.10                  .20                   .30
 │                    │                     │
 │                    │                     ├── Active Directory
 │                    │                     ├── DNS
 │                    │                     └── soclab.local
 │                    │
 │                    └── Manager
 │                        Indexer
 │                        Dashboard
 │
 └───────────────┐
                 │
                 ▼
             WIN-CLIENT
                .40
                 │
        ┌────────┴────────┐
        │                 │
   Windows Events       Sysmon
        │                 │
        └────────┬────────┘
                 │
           Wazuh Agent
                 │
                 ▼
          Wazuh Manager
```

## Next Steps

* Verify that normal Windows authentication events from WIN-CLIENT are being received by Wazuh.
* Confirm that Sysmon telemetry is reaching Wazuh.
* Explore Wazuh event and alert views.
* Generate controlled security events on WIN-CLIENT.
* Begin detection engineering.
* Begin controlled attack simulation from Kali.
* Develop the investigation and incident-response workflow.
* Remove the temporary NAT adapter once internet access is no longer required.
