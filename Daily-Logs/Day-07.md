# Daily Log — 2026/09/21

## Objectives

* Rebuild the Ubuntu Wazuh server with a clean installation.
* Allocate sufficient storage for the Wazuh environment.
* Install the Wazuh all-in-one deployment.
* Verify that the Wazuh components are functioning correctly.
* Prepare the Wazuh server for integration with the existing SOC lab network.

## Work Completed

### Task 1 — Rebuilt Ubuntu Wazuh Server

**What I did:**

* Reinstalled Ubuntu Server 24.04 on the Wazuh VM.
* Configured the virtual disk with approximately 100 GB of storage.
* Used the full available disk during the Ubuntu installation.
* Updated and upgraded the newly installed Ubuntu system.

**Why I did it:**

* The previous Ubuntu installation had insufficient allocated storage for the planned Wazuh deployment.
* A clean installation avoided complications from the previous partition and LVM configuration.
* Additional storage provides room for Wazuh logs, security events, alerts and indexed data as the lab grows.

### Task 2 — Verified Storage Configuration

**What I did:**

* Verified that the main Ubuntu partition was approximately 98 GB.
* Verified that the Ubuntu LVM volume group was approximately 98 GB.
* Confirmed that the volume group had no unallocated space remaining.

**Why I did it:**

* Wazuh can generate and retain a significant amount of security telemetry.
* Confirming the storage configuration before installing Wazuh reduces the risk of running out of disk space during future testing and attack simulations.

### Task 3 — Installed Wazuh

**What I did:**

* Downloaded the Wazuh 4.14 installation assistant.
* Verified that the downloaded file was a valid Bash script before executing it.
* Installed Wazuh using the all-in-one installation option.

**Why I did it:**

* The all-in-one deployment installs the required Wazuh components on a single server, which is appropriate for the isolated SOC homelab.
* Verifying the installer before execution ensured that the downloaded file was the expected installation script.

### Task 4 — Wazuh Core Components

**What I did:**

* Installed the Wazuh Indexer.
* Installed the Wazuh Manager.
* Installed the Wazuh Dashboard.
* Stored the generated Wazuh dashboard credentials securely.

**Why I did it:**

* The Wazuh Indexer stores and indexes security data.
* The Wazuh Manager processes agent data and handles security analysis and detection.
* The Wazuh Dashboard provides the web interface used to view alerts, events and security information.

### Task 5 — Verified Wazuh Services

**What I did:**

* Checked the status of the three main Wazuh services.
* Confirmed that:

  * Wazuh Indexer is active.
  * Wazuh Manager is active.
  * Wazuh Dashboard is active.

**Why I did it:**

* This confirmed that the core Wazuh deployment completed successfully and that the services are running before beginning agent and network configuration.

## Current Status

* Ubuntu Server 24.04 installed.
* Wazuh server VM configured with approximately 100 GB of storage.
* Wazuh Indexer installed and active.
* Wazuh Manager installed and active.
* Wazuh Dashboard installed and active.
* Wazuh administrator credentials securely stored.
* Wazuh server is ready for network configuration and endpoint integration.

## Next Steps

* Configure the Wazuh VM to use the SOC Host-Only network.
* Assign the planned static IP address `192.168.50.20`.
* Verify connectivity between Wazuh, DC01, Windows Client and Kali.
* Access and verify the Wazuh Dashboard.
* Install the Wazuh Agent on the Windows Client.
* Configure Windows/Sysmon telemetry collection.
* Verify that Windows security events are reaching Wazuh.
* Begin creating and testing the first SOC detections.
