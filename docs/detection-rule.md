# Detection Gap and Custom Rule

## What happened

The investigation successfully found Sysmon Event IDs 1 and 3 in `wazuh-archives-*`. These raw events showed the execution, `cmd.exe` child process, and the connection to Kali.

The initial investigation did not find a corresponding alert in `wazuh-alerts-*`. This does not mean the behavior did not happen. It means the raw telemetry was available, but no matching Wazuh alert was identified during the search.

## Improvement

The custom rule in ****.xml**** detects suspicious executable filenames such as:

```text
Invoice.pdf.exe
Report.docx.exe
Photo.jpg.scr
```

The rule looks for a double-extension executable launched from a user's Downloads folder.

## MITRE ATT&CK

- `T1036.007 - Double File Extension`

## Test the rule

1. Added the rule to `/var/ossec/etc/rules/local_rules.xml` on the Wazuh server.
2. The manager was restarted :

   ```bash
   sudo systemctl restart wazuh-manager
   ```

3. The authorized laboratory test was re-administered..
4. Search `wazuh-alerts-*`:

   ```text
   rule.id:"100100"
   ```
