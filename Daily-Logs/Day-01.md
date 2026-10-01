# Daily Log — 2026/08/08

## Objectives

* Install the initial virtual machines required for the SOC homelab
* Establish the initial structure of the virtual lab
* Prepare the systems that will later be used for security testing and monitoring

## Work Completed

* Installed and configured the Kali Linux virtual machine in VMware
* Installed and configured the Ubuntu Server virtual machine in VMware
* Established the initial roles of both virtual machines within the SOC homelab

### Task 1 — Install Kali Linux

**What I did:**

* Installed Kali Linux as a virtual machine inside VMware
* Completed the initial setup of the Kali Linux VM
* Established Kali Linux as the security testing workstation for the lab

**Why I did it:**

* Kali Linux will be used as the controlled security testing and attack workstation
* It will later be used to generate authorised security activity against the lab environment
* This activity will be used to test the SOC's monitoring and detection capabilities

### Task 2 — Install Ubuntu Server

**What I did:**

* Installed Ubuntu Server as a virtual machine inside VMware
* Completed the initial setup of the Ubuntu Server VM
* Established Ubuntu Server as the dedicated server for the future Wazuh deployment

**Why I did it:**

* Ubuntu Server will eventually host the Wazuh monitoring infrastructure
* Keeping the monitoring server separate from the systems being monitored provides a dedicated platform for log collection, detection and alerting

## Current Status

* Kali Linux VM installed
* Ubuntu Server VM installed
* Initial VMware lab structure established
* Host-Only network not yet configured
* Static IP addressing not yet configured
* Wazuh not installed
* Windows Server not configured
* Windows Client not configured
* Active Directory not configured
* Sysmon not configured

## Next Steps

* Configure the isolated VMware Host-Only network
* Establish the lab IP addressing scheme
* Configure Ubuntu Server with a persistent static IP
* Test connectivity between the lab systems
* Begin building the Windows Server and Active Directory environment


