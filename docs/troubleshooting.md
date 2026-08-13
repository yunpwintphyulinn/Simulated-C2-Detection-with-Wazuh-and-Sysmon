# Troubleshooting

Chronological notes from setting up the lab, cleaned up from the original working notes. Kept because these are genuinely useful the next time this environment needs to be rebuilt.

## Environment History

- First attempt used Ubuntu 26.04 as the Wazuh server, connecting via the host browser — Wazuh did not support this Ubuntu version at the time, so the server was rebuilt on **Ubuntu 24.04**.
- Ubuntu 24.04 **Server** edition, accessed via the host browser, was very laggy and required frequent reloads of the dashboard.
- Switched to **Ubuntu 24.04 Desktop** edition (16 GB RAM, 4 vCPU, 100 GB storage) running inside the VM itself — this resolved the responsiveness issue since the dashboard is accessed locally in the VM instead of proxied through the host browser.

- ## Ubuntu VM Loses Network After Restart

If the Ubuntu VM loses network connectivity after a restart, NetworkManager can be restarted:

```bash
sudo systemctl restart NetworkManager
```

The network configuration can then be checked:

```bash
ip -br -4 address
```
## Kali Static IP Does Not Return After Reboot

The Kali connection profile should be checked:

```bash
nmcli connection show
nmcli device status
ip -br -4 address
ip route
```

The correct saved profile can be brought up with:

```bash
sudo nmcli connection up kali-static
```

If this command is required after every reboot, the tester should verify that the intended connection profile is configured to autoconnect and that a second profile is not taking control of the network adapter.

## Wazuh Services Are Inactive

The Wazuh service status can be checked using the correct service names:

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
sudo systemctl status filebeat
```

If there is a fail service or services :

```bash
sudo systemctl restart wazuh-manager
sudo systemctl restart wazuh-indexer
```

## Wazuh Agent Is Inactive on Windows

In an elevated Windows PowerShell session, the Wazuh agent status and logs can be checked:

```powershell
Get-Service WazuhSvc
Restart-Service WazuhSvc
Get-Content 'C:\Program Files (x86)\ossec-agent\ossec.log' -Tail 50
```

The tester should confirm that the Windows agent is configured to connect to the Wazuh server at `192.168.100.20` and that the Windows VM can reach the server over the lab network.

## Sysmon Event Appears Locally but Not in Wazuh

The following configuration block should exist in the Windows Wazuh agent configuration file, `ossec.conf`:

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

After saving the configuration, the Wazuh agent should be restarted:

```powershell
Restart-Service WazuhSvc
```
**Sysmon events should be searched in `wazuh-archives-*`, not only in `wazuh-alerts-*`, because raw Sysmon events may not generate a default Wazuh alert.**
