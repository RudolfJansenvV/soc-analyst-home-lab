# SOC Analyst Home Lab

## Overview

This project documents the creation of a small Security Operations Centre lab using Wazuh, Windows 11, Sysmon and Microsoft Defender.

The lab is designed to develop practical experience with:

- SIEM monitoring
- Windows security logs
- Alert triage
- Endpoint monitoring
- Log analysis
- Incident investigation
- Phishing analysis
- MITRE ATT&CK
- Incident documentation

## Lab Architecture

The initial environment contains:

- Wazuh server and dashboard
- Windows 11 monitored endpoint
- Wazuh endpoint agent
- Sysmon
- Microsoft Defender

The systems are hosted in VirtualBox and connected through an isolated lab network.

## Project Stages

- [ ] Stage 1 — Build the monitoring environment
- [ ] Stage 2 — Generate and investigate security alerts
- [ ] Stage 3 — Conduct a phishing investigation
- [ ] Stage 4 — Create incident reports
- [ ] Stage 5 — Map activity to MITRE ATT&CK

## Setup Documentation

- [Wazuh server deployment](setup/README.md)
- [Windows endpoint deployment](setup/windows-endpoint.md)

## Current Progress

The Wazuh 4.14.7 server and Windows 11 endpoint have been deployed in VirtualBox. Both systems use an isolated host-only network, and private communication between `WIN11-SOC` and the Wazuh server has been verified successfully.

The next step is installing and registering the Wazuh agent on the Windows endpoint.

See the [Wazuh server setup](setup/README.md) and [Windows endpoint setup](setup/windows-endpoint.md) for the deployment process.

- [Investigation 01: Encoded PowerShell Execution](investigations/01-encoded-powershell.md)
