# 2026-09-24

## Work Completed

### Wazuh Sysmon Telemetry Integration

* Verified the Wazuh Agent configuration on `WIN-CLIENT`.
* Confirmed Windows Security, Application and System event collection was already configured.
* Identified that the Sysmon Operational event channel was not yet configured for Wazuh collection.
* Added the following Sysmon event channel to the Wazuh Agent configuration:

```text
Microsoft-Windows-Sysmon/Operational
```

* Restarted the `WazuhSvc` service and verified it was running.
* Confirmed Sysmon was generating process creation events using Event ID 1.
* Verified the Wazuh Manager, Wazuh Indexer and Wazuh Dashboard services were operational after resolving a temporary Wazuh Manager startup timeout by restarting the service.
* Generated test activity on `WIN-CLIENT` and confirmed the resulting Sysmon telemetry was visible in the Wazuh Dashboard.
* Verified a Sysmon Event ID 11 event in Wazuh with:

  * Provider: `Microsoft-Windows-Sysmon`
  * Channel: `Microsoft-Windows-Sysmon/Operational`
  * Event ID: `11`
* Confirmed the complete telemetry pipeline:

```text
WIN-CLIENT
    ↓
Sysmon
    ↓
Sysmon Operational Log
    ↓
Wazuh Agent
    ↓
Wazuh Manager
    ↓
Wazuh Indexer
    ↓
Wazuh Dashboard
```

### Initial Detection Engineering

* Began the first detection engineering exercise focused on PowerShell execution.
* Generated a PowerShell process on `WIN-CLIENT` and located the corresponding Sysmon Event ID 1 in Wazuh.
* Inspected the telemetry fields available in the event, including:

  * `data.win.eventdata.image`
  * `data.win.eventdata.commandLine`
  * `data.win.eventdata.parentImage`
  * `data.win.eventdata.parentCommandLine`
  * `data.win.eventdata.user`
  * `data.win.eventdata.integrityLevel`
  * Process and parent process IDs
  * File hashes
* Identified the PowerShell process as:

```text
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

* Confirmed the test PowerShell process was executed by `SOCLAB\LabUser` with a Medium integrity level.
* Identified that the test created a PowerShell → PowerShell parent/child process relationship.
* Investigated the Wazuh alert generated for this activity.
* Examined built-in Wazuh rule `92027`, described as:

```text
Powershell process spawned powershell instance
```

* Established that rule `92027` uses:

  * `if_group` to target Sysmon Event ID 1 events.
  * A PCRE2 pattern against `win.eventdata.image` to identify PowerShell.
  * A PCRE2 pattern against `win.eventdata.parentImage` to identify PowerShell as the parent process.
  * `(?i)` for case-insensitive matching.
* Confirmed that both conditions must match for the rule to generate an alert.
* Identified the rule's MITRE ATT&CK mapping:

  * Technique: PowerShell
  * Technique ID: `T1059.001`
  * Tactic: Execution
* Learned the distinction between endpoint telemetry and detection logic.
* Established that `data.win.eventdata.commandLine` is the relevant field for detecting specific PowerShell command-line arguments.
* Discussed how combining multiple conditions with AND logic can make detections more specific and reduce unnecessary alerts.

## Key Learning

Established the difference between Windows Security events and Sysmon events.

Example:

```text
Windows Security Event 4688
→ Process creation

Sysmon Event 1
→ Process creation

Sysmon Event 11
→ File creation
```

Established that Sysmon Event ID 1 provides detailed process telemetry that can be used for detection engineering, including:

```text
Process
Command Line
Parent Process
User
Integrity Level
Hashes
Process IDs
```

Established the distinction between:

```text
Telemetry
→ Raw security-relevant information collected from an endpoint

Detection
→ Logic applied to telemetry to identify potentially significant behaviour

Alert
→ The result produced when detection conditions are satisfied
```

Also established that simply detecting PowerShell execution would be noisy because PowerShell is a legitimate Windows administration tool. More specific behavioural conditions can produce more useful detections.

## Current Status

The core telemetry pipeline is operational.

The project has now moved from infrastructure deployment and telemetry validation into **detection engineering**.

The first PowerShell detection exercise has been completed using an existing Wazuh rule as a learning example.

The next planned step is to inspect the Wazuh custom rule environment and begin building a custom detection based on PowerShell command-line activity.

Kali-based attack simulation has not yet begun. The defensive telemetry and detection foundations are being established first so that subsequent activity generated from Kali can be observed, detected, investigated and documented through the SOC workflow.
