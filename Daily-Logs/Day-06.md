# Daily Log — 2026/09/18

## Objectives

* Complete the Windows Client setup.
* Join the Windows Client to the Active Directory domain.
* Verify Group Policy is being applied.
* Install and verify Sysmon.
* Restore the Windows Client to the isolated SOC network.
* Ensure the Kali Linux VM remains functional after accidentally moving its VM files.

## Work Completed

### Task 1 — Complete Windows Client Configuration

**What I did:**

* Configured the Windows Client as `WIN-CLIENT`.
* Configured the static IP address as `192.168.50.40`.
* Configured the subnet mask as `255.255.255.0`.
* Configured the Domain Controller (`192.168.50.30`) as the DNS server.
* Verified connectivity between the Windows Client and the Domain Controller.
* Verified DNS resolution for the `soclab.local` domain.

**Why I did it:**

* The Windows Client is required as an endpoint within the simulated enterprise environment.
* The client needs reliable connectivity to the Domain Controller for Active Directory authentication, DNS and Group Policy.

### Task 2 — Join Windows Client to Active Directory

**What I did:**

* Joined `WIN-CLIENT` to the `soclab.local` Active Directory domain.
* Logged into the domain using the `SOCLAB\labuser` account.
* Used the `whoami` command to verify the account being used.
* Confirmed that the output was:

```text
soclab\labuser
```

* Identified that `WIN-CLIENT\labuser` and `SOCLAB\labuser` are separate accounts.

**Why I did it:**

* Domain joining allows the Windows Client to operate as an enterprise endpoint managed through Active Directory.
* Using a domain account allows authentication and security activity to be tested in a realistic enterprise environment.

### Task 3 — Apply and Verify Group Policy

**What I did:**

* Moved the `WIN-CLIENT` computer object into the `Lab-Computers` OU.
* Forced a Group Policy update using:

```text
gpupdate /force
```

* Used `gpresult` to check the policies applied to the client.
* Initially received an unexpected `N/A` result when checking the applied policies.
* Performed a more specific computer policy check using:

```text
gpresult /scope computer /r
```

* Confirmed that `Lab - Security Baseline` appeared under **Applied Group Policy Objects**.

**Why I did it:**

* The security baseline GPO contains the auditing policies required to generate useful security events.
* Verifying that the GPO is applied confirms that the Windows Client is receiving the intended security configuration from Active Directory.

### Task 4 — Install and Verify Sys
