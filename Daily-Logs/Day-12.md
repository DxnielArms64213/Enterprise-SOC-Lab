# 2026-09-29

## Work Completed

Continued detection engineering within the Enterprise SOC Homelab.

### Windows Failed Logon Detection

Continued development of custom Wazuh rule `100003` for detecting Windows failed interactive logons caused by an incorrect password.

The Windows Security event was confirmed to contain:

* **Event ID:** `4625`
* **Sub-status:** `0xc000006a`
* **Provider:** `Microsoft-Windows-Security-Auditing`

The existing Wazuh rule `60122` was identified as the built-in detection responsible for detecting the failed Windows logon.

The custom rule was modified to build upon rule `60122` using `if_sid` rather than independently matching the entire event.

The final rule structure is:

```xml
<rule id="100003" level="5">
    <if_sid>60122</if_sid>
    <field name="win.eventdata.subStatus">0xc000006a</field>
    <description>Windows interactive logon failed due to incorrect password</description>
</rule>
```

### Rule Validation

The Wazuh analysis configuration was validated successfully using:

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
```

No configuration errors were returned.

The Wazuh Manager was restarted and verified as active.

### Detection Testing

A deliberate incorrect-password attempt was generated on `WIN-CLIENT`.

The resulting Windows failed-logon event was successfully detected by the custom Wazuh rule:

```text
Rule ID: 100003
```

This confirmed that the custom detection correctly identifies failed interactive logons where the failure reason is an incorrect password.

The detection chain is now:

```text
Incorrect Password
       ↓
Windows Event 4625
       ↓
Wazuh Rule 60122
       ↓
Sub-status 0xc000006a
       ↓
Custom Rule 100003
       ↓
Wazuh Alert
```

## Current Detection Status

Completed detections:

| Rule ID  | Detection                                      | Status    |
| -------- | ---------------------------------------------- | --------- |
| `100002` | PowerShell encoded command                     | Validated |
| `100003` | Windows failed logon due to incorrect password | Validated |

The negative test for `100003` was not completed during this session and remains optional future testing.

## Next Steps

Continue detection engineering by developing the next useful Windows/Sysmon detection and subsequently begin controlled attack simulations from Kali.
