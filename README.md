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

- [x] Stage 1 — Build the monitoring environment
- [x] Stage 2 — Generate and investigate security alerts
- [x] Stage 3 — Conduct a phishing investigation
- [x] Stage 4 — Create incident reports
- [x] Stage 5 — Map activity to MITRE ATT&CK

## Setup Documentation

- [Wazuh server deployment](setup/README.md)
- [Windows endpoint deployment](setup/windows-endpoint.md)

## Current Progress

The SOC Analyst Home Lab is operational and the initial project roadmap is complete.

The environment includes a Wazuh 4.14.7 server, a monitored Windows 11 endpoint, Sysmon telemetry and an isolated VirtualBox network. The project now contains:

- Two documented endpoint investigations
- A credential-phishing analysis
- A formal incident report
- A custom Wazuh correlation rule
- Consolidated MITRE ATT&CK coverage
- Supporting screenshots and preserved evidence

The lab demonstrates practical experience with SIEM monitoring, Windows event analysis, alert triage, detection engineering, phishing analysis, incident documentation and MITRE ATT&CK mapping.

See the [Wazuh server setup](setup/README.md) and [Windows endpoint setup](setup/windows-endpoint.md) for the deployment process.

## Investigations

- [Investigation 01: Encoded PowerShell Execution](investigations/01-encoded-powershell.md)
- [Investigation 02: Repeated Failed Windows Logons](investigations/02-repeated-failed-logons.md)
- [Phishing Case 01: Microsoft 365 Credential Phishing](phishing-analysis/01-credential-phishing-email.md)
- [Incident Report IR-001: Encoded PowerShell Execution](incident-reports/IR-001-encoded-powershell.md)

## Detections and ATT&CK Coverage

- [Windows failed-logon correlation rule](detections/windows-auth-correlation-rule.xml)
- [MITRE ATT&CK coverage](detections/mitre-attack-coverage.md)
