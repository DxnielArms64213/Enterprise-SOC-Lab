# Daily Log — 2026/09/10

## Objectives

* Configure the isolated VMware Host-Only network
* Configure the Ubuntu Server VM
* Give Ubuntu Server a persistent static IP
* Test network connectivity
* Prepare Ubuntu Server for the future Wazuh installation

## Work Completed

* Configured VMware VMnet1 as a Host-Only network
* Set the Host-Only subnet to `192.168.50.0/24`
* Disabled VMware DHCP
* Identified Ubuntu's network interface as `ens33`
* Configured Ubuntu Server with the static IP `192.168.50.20/24`
* Configured Netplan to use NetworkManager
* Successfully applied the network configuration
* Tested connectivity between Ubuntu Server and the Windows host
* Rebooted Ubuntu Server and confirmed that the static IP persisted

### Task 1 — Configure the Host-Only Network

**What I did:**

* Configured VMware VMnet1 as a Host-Only network
* Set the subnet to `192.168.50.0/24`
* Disabled VMware DHCP
* Established the initial IP addressing scheme for the lab

**Why I did it:**

* The SOC homelab requires an isolated network where the virtual machines can communicate with each other
* A Host-Only network provides a controlled environment for future security testing and attack simulations

### Task 2 — Configure Ubuntu's Network Interface

**What I did:**

* Identified Ubuntu's network interface as `ens33`
* Investigated the existing Netplan configuration
* Confirmed that IPv4 was initially using DHCP
* Changed the configuration to use a static IPv4 address

**Why I did it:**

* The future Wazuh server requires a predictable IP address
* Other systems in the lab will eventually need to know where to send security telemetry and logs

### Task 3 — Configure Ubuntu's Static IP

**What I did:**

* Assigned Ubuntu Server the static IP `192.168.50.20/24`
* Configured Netplan to use NetworkManager as its renderer
* Generated and applied the Netplan configuration successfully

**Why I did it:**

* A static IP prevents the server's address from changing
* This will make the Ubuntu Server easier to configure and access when Wazuh is installed

### Task 4 — Troubleshoot the Network Configuration

**What I did:**

* Investigated an error where Netplan attempted to use `systemd-networkd`
* Confirmed that Ubuntu was using NetworkManager
* Added `renderer: NetworkManager` to the Netplan configuration
* Successfully applied the corrected configuration

**Why I did it:**

* Netplan needed to use the correct networking backend
* Troubleshooting the configuration provided practical experience with Linux network configuration

### Task 5 — Test Network Connectivity

**What I did:**

* Tested connectivity between Ubuntu Server and the Windows host at `192.168.50.1`
* Confirmed that the Windows host could successfully ping Ubuntu Server at `192.168.50.20`
* Found that Ubuntu Server could not receive ping replies from the Windows host

**Why I did it:**

* Network connectivity needs to be verified before building the rest of the lab
* Testing both directions helps identify potential networking and firewall issues

**Result at the end of Day 02:**

* Windows Host → Ubuntu Server: Successful
* Ubuntu Server → Windows Host: Not yet working

The remaining connectivity issue was carried forward to the following day for troubleshooting.

### Task 6 — Verify the Static IP After Reboot

**What I did:**

* Shut down and restarted the Ubuntu Server VM
* Checked the `ens33` interface after the restart
* Confirmed that `192.168.50.20/24` was still assigned

**Why I did it:**

* This confirmed that the static IP configuration was persistent
* Ubuntu Server was ready for the next stage of the SOC homelab

## Current Status

* Kali Linux installed
* Ubuntu Server installed
* VMware VMnet1 Host-Only network configured
* DHCP disabled
* Ubuntu `ens33` identified
* Ubuntu Server static IP configured: `192.168.50.20/24`
* Static IP survives reboot
* Windows Host → Ubuntu Server connectivity confirmed
* Ubuntu Server → Windows Host connectivity still requires troubleshooting
* Wazuh not installed
* Windows Server not configured
* Windows Client not configured
* Active Directory not configured
* Sysmon not configured

## Next Steps

* Troubleshoot Ubuntu Server → Windows Host connectivity
* Configure the Windows Server VM
* Assign Windows Server the static IP `192.168.50.30/24`
* Install and configure Active Directory Domain Services
* Configure DNS
* Begin building the Windows domain environment
