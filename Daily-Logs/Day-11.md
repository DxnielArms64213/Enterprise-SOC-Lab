# Daily Log — 2026-09-28

## Work Completed

Continued detection engineering and testing within the Enterprise SOC Homelab.

### Custom Wazuh Detection — PowerShell Encoded Commands

Continued troubleshooting the custom Wazuh rule `100002`, designed to detect PowerShell processes using encoded commands.

The relevant Sysmon Event ID 1 alert was inspected in the Wazuh Dashboard. The event was confirmed to use:

* **Rule ID:** `92027`
* **Rule Level:** `4`
* **Rule Groups:** `sysmon`, `sysmon_eid1_detections`, `windows`
* **Process Image:** PowerShell
* **Command Line:** contained `encodedcommand`

The initial custom rule did not trigger because the rule conditions were not matching the event as expected.

The detection was simplified to isolate the matching conditions. The working rule was then built using the `sysmon_eid1_detections` group and a case-insensitive PCRE2 match for `encodedcommand`.

The rule was subsequently made more specific by adding a condition matching:

```text
powershell\.exe
```

This ensured that the detection identifies encoded PowerShell execution rather than simply matching the presence of the `encodedcommand` string.

### Detection Rule

The final tested rule was:

```xml
<rule id="100002" level="5">
    <if_group>sysmon_eid1_detections</if_group>
    <field name="win.eventdata.image" type="pcre2">powershell\.exe</field>
    <field name="win.eventdata.commandLine" type="pcre2">(?i)encodedcommand</field>
    <description>PowerShell process using encoded command</description>
</rule>
```

The Wazuh configuration was validated successfully, the Wazuh Manager was restarted, and all relevant services remained active.

### Positive Detection Test

A controlled PowerShell encoded-command test was performed on `WIN-CLIENT`.

The Wazuh Dashboard generated:

```text
Rule ID: 100002
Rule Level: 5
```

This confirmed that the custom detection successfully matched the intended behaviour.

### Negative Detection Test

A second test was performed using a normal PowerShell command without an encoded command.

The test generated normal Sysmon process telemetry, but **rule `100002` did not trigger**.

This confirmed that the detection was not simply alerting on every PowerShell process.

## Detection Engineering Workflow

The completed workflow was:

```text
Identify Behaviour
        ↓
Inspect Wazuh Telemetry
        ↓
Create Detection Rule
        ↓
Troubleshoot Rule Matching
        ↓
Refine Detection Conditions
        ↓
Positive Test
        ↓
Negative Test
        ↓
Validate Detection
```

This provided practical experience with Wazuh rule development, Sysmon telemetry, PCRE2 field matching and detection validation.

## Next Steps

The next detection will focus on **failed Windows logons** using Windows Security Event ID `4625`.

The plan is to first generate controlled failed authentication events on `WIN-CLIENT`, inspect the telemetry received by Wazuh, and then develop a detection based on the observed event data.
