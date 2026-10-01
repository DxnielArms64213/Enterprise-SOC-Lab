# Enterprise SOC Homelab — 2026-09-26

## Work Completed

### Custom Wazuh Detection Testing

Continued detection engineering within the Enterprise SOC Homelab by testing the custom Wazuh detection rule `100002`.

The rule is designed to identify PowerShell processes using the `EncodedCommand` parameter.

The detection currently uses:

* Sysmon Event ID 1
* `win.eventdata.image` matching `powershell.exe`
* `win.eventdata.commandLine` matching `encodedcommand`

The rule was modified to use a case-insensitive PCRE2 expression for the command-line condition:

```xml
<field name="win.eventdata.commandLine" type="pcre2">(?i)encodedcommand</field>
```

### Wazuh Rule Validation

The modified Wazuh rules were validated using:

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
```

The command returned to the terminal without an error, confirming that the rule syntax was valid.

The Wazuh Manager was then restarted:

```bash
sudo systemctl restart wazuh-manager
```

Its status was verified using:

```bash
sudo systemctl is-active wazuh-manager
```

The service returned:

```text
active
```

### Sysmon Event Verification

A controlled PowerShell process was generated on `WIN-CLIENT` using the `-EncodedCommand` parameter.

The resulting Sysmon Event ID 1 was successfully collected by Wazuh.

The event contained the expected fields:

```text
data.win.eventdata.image
data.win.eventdata.commandLine
```

The image field identified:

```text
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

The command-line field contained the PowerShell executable followed by the encoded command parameter and test string.

This confirmed that the Windows endpoint was generating the required telemetry and that Wazuh was successfully receiving and parsing the Sysmon event.

### Detection Testing Result

The custom rule `100002` did not generate an alert in the Wazuh Dashboard despite the required PowerShell event being received.

This narrowed the issue to the rule-matching stage rather than the Windows or Wazuh Agent telemetry pipeline.

The investigation was therefore paused before making further rule changes.

The next troubleshooting step will be to inspect the event's Wazuh rule/group information and determine whether the `sysmon_event1` group is being assigned correctly.

## Key Learning

Today's work reinforced the difference between:

* Endpoint event generation
* Sysmon telemetry
* Wazuh Agent collection
* Wazuh event parsing
* Rule matching
* Alert generation

The Windows Client and Wazuh telemetry pipeline is functioning correctly. The remaining issue is specifically determining why the custom rule is not matching the received Sysmon Event ID 1.

## Current Status

```text
Windows PowerShell Test
        ↓
Sysmon Event ID 1          ✓
        ↓
Wazuh Agent                ✓
        ↓
Wazuh Manager              ✓
        ↓
Sysmon Event in Dashboard  ✓
        ↓
Custom Rule 100002         ✗
        ↓
Wazuh Alert
```

The custom detection has been created and validated syntactically, but it has **not yet been successfully triggered**.

## Next Session

The next session will continue troubleshooting rule `100002`.

The first step will be to inspect the Wazuh event's rule/group information and verify whether the event belongs to the `sysmon_event1` group required by:

```xml
<if_group>sysmon_event1</if_group>
```

No further changes will be made to the detection rule until this has been verified.
