# Investigation Report — Case LAB-C2-001

## 1. Executive Summary

On 2026-08-11, an authorized simulated intrusion was conducted in an isolated home lab. A Meterpreter reverse-TCP payload (`Test.pdf.exe`), disguised with a double file extension, was delivered to a Windows 10 endpoint (`windows10-victim`) and executed by the simulated user. The payload established a command-and-control (C2) connection back to an attacker-controlled host (Kali, `192.168.100.10:4444`) and spawned an interactive shell. Sysmon captured the attack chain and forwarded it to Wazuh, but **the default Wazuh ruleset did not generate an alert** for this activity — the telemetry existed only in raw archives, not in the alert feed. A custom detection rule was subsequently written and deployed to close this gap.
