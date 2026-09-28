# MITRE ATT&CK Coverage

## Overview

This document maps the security activity investigated in the SOC Analyst Home Lab to the MITRE ATT&CK Enterprise framework.

The mappings distinguish between behavior directly observed in the evidence and activity that was only identified as a suspected attacker objective.

An ATT&CK mapping describes behavior. It does not prove that the activity was malicious.

## Evidence-status definitions

| Status | Meaning |
|---|---|
| Observed | The behavior was directly present in collected evidence |
| Simulated | The behavior was deliberately generated during an authorized lab exercise |
| Suspected objective | The likely attacker objective was inferred but not directly observed |
| Not covered | The tactic or technique has not yet been tested in the lab |

## Coverage summary

| Tactic | Technique | Name | Evidence status | Primary evidence |
|---|---|---|---|---|
| Execution | `T1059.001` | Command and Scripting Interpreter: PowerShell | Observed — authorized simulation | Sysmon Event ID 1 and Wazuh rule `92057` |
| Credential Access | `T1110.001` | Brute Force: Password Guessing | Observed — authorized simulation | Windows Event ID `4625` and Wazuh rules `60122`/`100102` |
| Initial Access | `T1566.002` | Phishing: Spearphishing Link | Observed — synthetic email | Email headers, body and deceptive URL |
| Credential Access / Collection | `T1056.003` | Input Capture: Web Portal Capture | Suspected objective | Credential-phishing pretext and link |

## T1059.001 — PowerShell

### Tactic

Execution

### Evidence status

Observed during an authorized lab simulation.

### Activity

PowerShell launched another PowerShell process using the `-EncodedCommand` parameter.

The Base64 payload decoded to:

```powershell
Write-Output 'SOC-LAB-ENCODED-TEST'
```

## Telemetry
|Source |	Evidence|
|---|---|
|Sysmon |	Event ID 1 — Process creation|
|Wazuh	| Built-in rule `92057`|
|Wazuh severity	| Level `12`|
|Endpoint |	`WIN11-SOC`|
|User	| `WIN11-SOC\PC_1`|


## Detection value
The detection identified potentially concealed PowerShell execution and preserved the complete process chain and command line for analysis.
The decoded payload was harmless, but the behavioral mapping remained valid because PowerShell execution was directly observed.

### Related documentation
- [Investigation 01: Encoded PowerShell Execution](../investigations/01-encoded-powershell.md)
- [Incident Report IR-001: Encoded PowerShell Execution](../incident-reports/IR-001-encoded-powershell.md)

## T1110.001 — Password Guessing
### Tactic
Credential Access

### Evidence status
Observed during an authorized lab simulation.

### Activity
Five incorrect passwords were submitted against the same valid local account within several seconds.

Windows recorded:
```text
Event ID: 4625
Status: 0xC000006D
Substatus: 0xC000006A
Logon type: 2
```

The status and substatus confirmed a failed logon involving a valid account and an incorrect password.

## Telemetry
|Source |	Evidence|
|---|---|
|Windows Security log |	Event ID `4625`|
|Built-in Wazuh detection |	Rule `60122`, level `5`|
|Custom Wazuh correlation	| Rule `100102`, level `10`|
|Correlation threshold |	Five failures for the same account within 60 seconds|
|Endpoint |	`WIN11-SOC`
|Target account |	`SOC-LAB-TEST`|


## Detection improvement
The built-in Wazuh rule detected each failed logon separately. A custom correlation rule was created to identify the repeated pattern and elevate it to level 10.

## Related documentation
- [Investigation 02: Repeated Failed Windows Logons](../investigations/02-repeated-failed-logons.md)
- [Custom Windows authentication correlation rule](windows-auth-correlation-rule.xml)

## T1566.002 — Spearphishing Link

### Tactic
Initial Access

### Evidence status
Observed in a safe synthetic phishing email.

### Activity
An email impersonating Microsoft 365 Security attempted to convince the recipient to open a deceptive account-verification link.

The email included:

- A lookalike sender domain.
- Mismatched From, Reply-To and Return-Path domains.
- Failed SPF and DMARC validation.
- No DKIM signature.
- Urgency and a threat of account suspension.
- A deceptive link resembling a Microsoft login address.

## Evidence
|Source |	Finding|
|---|---|
|Email headers |	Sender and authentication anomalies|
|Email body | Urgency, impersonation and credential-confirmation request|
|URL |	Deceptive non-Microsoft hostname|
|SHA256	| Original email preserved and hashed|
|User interaction |	Not observed|


### Related documentation
- [Phishing Case 01: Microsoft 365 Credential Phishing](../phishing-analysis/01-credential-phishing-email.md)
- [Preserved email evidence](../phishing-analysis/evidence/01-password-expiry.eml)

## T1056.003 — Web Portal Capture

### Tactics
Credential Access and Collection

### Evidence status
Suspected objective only.

### Assessment
The phishing email attempted to direct the recipient to an account-verification page. This behavior was consistent with an attempt to capture credentials through a deceptive web portal.
The URL was not opened, no portal was examined and no credential submission occurred. Web Portal Capture was therefore not recorded as directly observed activity.
This distinction prevents the report from presenting an inferred objective as confirmed evidence.

## Detection coverage
|Data source |	Coverage provided|
|---|---|
|Sysmon |	Process creation and command-line telemetry|
|Windows Security log	| Authentication success and failure activity|
|Wazuh built-in rules |	Encoded PowerShell and individual logon failures|
|Wazuh custom rules |	Repeated failed-logon correlation|
|Email headers |	Sender identity, routing and authentication analysis|
|Email body |	Social-engineering and URL analysis|
|PowerShell |	Evidence hashing and safe Base64 decoding|


## Covered ATT&CK tactics
|Tactic |	Coverage|
|---|---|
|Initial Access |	Spearphishing link|
|Execution | PowerShell|
|Credential Access |	Password guessing|
|Collection	| Suspected web portal credential capture|


## Current coverage gaps
The lab has not yet directly tested:
- Persistence
- Privilege Escalation
- Defense Evasion
- Discovery
- Lateral Movement
- Command and Control
- Exfiltration
- Impact
The current environment also has limited coverage for:
- Network traffic analysis
- DNS monitoring
- Web-proxy telemetry
- Cloud identity logs
- Email click tracking
- Memory analysis
- Malware detonation
These gaps define possible future lab expansions rather than failures in the existing investigations.

## Key lessons
- High-severity detections require contextual investigation.
- ATT&CK mappings describe behavior and do not automatically establish malicious intent.
- Directly observed behavior should be distinguished from suspected attacker objectives.
- Individual alerts can require correlation before a larger attack pattern becomes visible.
- Preserving original telemetry and calculating hashes supports evidence integrity.
- Custom detection rules can close visibility gaps identified during investigations.

## Conclusion

The SOC Analyst Home Lab currently provides documented coverage across Initial Access, Execution, Credential Access and limited Collection activity.
The investigations demonstrate endpoint monitoring, Windows log analysis, phishing analysis, alert triage, detection engineering, evidence preservation and formal incident reporting.
Future exercises can expand coverage into persistence, privilege escalation, defense evasion, lateral movement, command and control, exfiltration and impact.
