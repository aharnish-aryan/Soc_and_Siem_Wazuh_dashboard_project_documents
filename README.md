# Soc_and_Siem_Wazuh_dashboard_project_documents
📘 README — SOC & SIEM Dashboard Setup (Wazuh)
📌 Project Overview
This project demonstrates the deployment and configuration of an open-source SIEM solution (Wazuh) in a multi-VM lab environment. It centralizes log collection, normalizes security events, and provides real-time dashboards for monitoring authentication trends and detecting brute-force attacks (MITRE ATT&CK T1110).

🛠️ Tools & Environment
Oracle VirtualBox v7.x → Virtualization platform

Ubuntu Server 22.04/24.04 LTS → Wazuh Manager, Indexer, Dashboard

Windows 10 (Evaluation ISO) → Endpoint agent for log forwarding

Wazuh v4.14 → SIEM platform

PowerShell & auditpol → Configuring Windows audit policies

Lab Setup:

Ubuntu VM: 192.168.29.94 (Wazuh Server)

Windows VM: 192.168.29.229 (Agent endpoint)

⚙️ Installation & Configuration Steps
Install Wazuh Server (Ubuntu VM):

bash
sudo apt update && sudo apt upgrade -y
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
Deploy Wazuh Agent (Windows VM):

powershell
msiexec.exe /i wazuh-agent-4.14.4-1.msi /q WAZUH_MANAGER="192.168.29.94" WAZUH_AGENT_NAME="win10-agent"
NET START WazuhSvc
Configure Windows Audit Policies:

powershell
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
auditpol /set /subcategory:"Logoff" /success:enable
auditpol /set /subcategory:"Account Lockout" /failure:enable
Customize Dashboards:

Build SOC Overview dashboard (failed logins, top source IPs, event distribution).

Create Custom Brute-Force Detection Rule (MITRE T1110):

xml
<group name="windows,bruteforce">
  <rule id="100200" level="12" frequency="5" timeframe="60" ignore="60">
    <if_matched_sid>60122</if_matched_sid>
    <same_field>data.srcip</same_field>
    <description>Multiple failed Windows logons from same source (possible brute-force).</description>
    <mitre><id>T1110</id></mitre>
  </rule>
</group>
Validate via Attack Simulation:

Perform controlled failed login attempts.

Confirm alerts in alerts.json and dashboard visualization.

✅ Results
Log Normalization: Windows Event ID 4625 parsed into structured fields.

Authentication Tracking: Real-time logs from Linux & Windows endpoints.

Brute-Force Detection: Custom rule triggered successfully (Severity Level 12).

Dashboard Visualization: SOC Overview updated dynamically with attack data.

🚧 Challenges & Solutions
Agent Timeout: Fixed by switching VirtualBox NAT → Bridged Adapter.

Missing Logs: Re-applied auditpol with elevated privileges.

Rule Syntax Errors: Validated using wazuh-logtest before restarting manager.

🎯 Learning Outcomes
Hands-on SIEM deployment and troubleshooting.

Advanced Windows audit policy configuration.

Log normalization and custom rule engineering.

SOC dashboard design for real-time monitoring.

🔮 Future Scope
Integrate active response scripts (auto-block malicious IPs).

Connect SIEM alerts to ticketing platforms (TheHive, Slack webhooks).

Expand to cloud security telemetry (AWS, GCP).
