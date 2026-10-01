# 2026-09-30

## Detection Engineering

Continued development and testing of custom Wazuh detection rules.

### Detection Rule 100004

Completed development of detection rule `100004` for identifying `whoami.exe` being executed from `cmd.exe`.

The detection was configured to use the `sysmon` rule group and match:

* `win.eventdata.image` containing `whoami.exe`
* `win.eventdata.parentImage` containing `cmd.exe`

The final rule was:

```xml
<rule id="100004" level="5">
    <if_group>sysmon</if_group>
    <field name="win.eventdata.image" type="pcre2">(?i)whoami\.exe</field>
    <field name="win.eventdata.parentImage" type="pcre2">(?i)cmd\.exe</field>
    <description>whoami.exe executed from cmd.exe</description>
</rule>
```

The rule was validated using the Wazuh analysis configuration test before restarting the Wazuh Manager.

### Positive Test

The following command was executed on `WIN-CLIENT`:

```powershell
cmd.exe /c whoami
```

The resulting Sysmon event contained:

```text
image: C:\Windows\System32\whoami.exe
parentImage: C:\Windows\System32\cmd.exe
```

Wazuh successfully generated custom alert `100004`.

### Negative Test

A second test was performed by executing:

```powershell
whoami.exe
```

directly from PowerShell.

The `whoami.exe` process was recorded by Sysmon, but Wazuh did not generate alert `100004`.

This confirmed that the detection requires the `cmd.exe` parent process rather than simply detecting execution of `whoami.exe`.

## Detection Status

Three custom Wazuh detections have now been successfully developed and validated with both positive and negative tests:

| Rule     | Detection                                   | Status    |
| -------- | ------------------------------------------- | --------- |
| `100002` | PowerShell encoded command                  | Validated |
| `100003` | Incorrect password during interactive logon | Validated |
| `100004` | `whoami.exe` executed from `cmd.exe`        | Validated |

The detection engineering phase is progressing from basic rule creation into testing detection logic against realistic Windows endpoint telemetry.

## Next Steps

Continue developing additional meaningful detections before moving into controlled attack simulations and full SOC investigation workflows.

Planned workflow:

```text
Detection Engineering
        ↓
Additional Detections
        ↓
Kali Attack Simulation
        ↓
Wazuh Alert
        ↓
Triage
        ↓
Investigation
        ↓
MITRE ATT&CK Mapping
        ↓
Incident Documentation
```
