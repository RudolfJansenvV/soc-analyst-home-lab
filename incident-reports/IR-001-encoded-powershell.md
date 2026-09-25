# Incident Report IR-001: Encoded PowerShell Execution

## Incident overview

| Field | Value |
|---|---|
| Incident ID | `IR-001` |
| Date detected | 23 September 2026 |
| Detection time | Approximately 11:39 SAST |
| Status | Closed |
| Affected asset | `WIN11-SOC` |
| Endpoint IP | `192.168.56.102` |
| Affected user | `WIN11-SOC\PC_1` |
| Detection source | Wazuh and Sysmon |
| Initial severity | High |
| Final contextual severity | Informational |
| Classification | True positive — authorized benign activity |
| MITRE ATT&CK | `T1059.001` — PowerShell |
| Containment required | No |
| Escalation required | No |

## Executive summary

Wazuh generated a high-severity alert after PowerShell launched another PowerShell process using the `-EncodedCommand` parameter on the `WIN11-SOC` endpoint.

Sysmon recorded the process creation as Event ID `1`, and Wazuh's built-in rule `92057` identified the encoded execution at alert level `12`. The command was investigated because encoded PowerShell can conceal malicious activity.

The Base64 payload was decoded safely and contained only an authorized lab command that printed the text `SOC-LAB-ENCODED-TEST`. No malicious payload, persistence, credential access, network activity or unauthorized system modification was identified.

The alert was therefore classified as a true positive with authorized benign activity. No containment or escalation was required.

## Detection

The incident was detected through the following monitoring pipeline:

```text
PowerShell execution
        ↓
Sysmon Event ID 1
        ↓
Wazuh agent
        ↓
Wazuh rule 92057
        ↓
Level 12 alert
```

Wazuh generated the following alert:

Detection details
|Field |	Value |
|---|---|
|Wazuh rule |	`92057` |
|Rule level |	`12` |
|Description	PowerShell spawned a PowerShell process which executed a Base64-encoded command |
|Event source |	`Microsoft-Windows-Sysmon/Operational` |
|Sysmon event ID |	`1` — Process creation |
|Provider |	`Microsoft-Windows-Sysmon` |
|Agent ID |	`001` |

## Incident timeline
|Time |	Event |
|---|---|
|`09:39:00.716 UTC` |	Sysmon recorded the PowerShell child-process creation |
|Approximately `11:39 SAST` |	Wazuh generated rule `92057` at level `12` |
|After detection |	The process tree, command line, user and executable hash were reviewed |
|During analysis |	The Base64 payload was decoded without executing it |
|During scoping |	No malicious child processes, downloads, persistence or system changes were identified |
|After validation |	The activity was confirmed as an authorized lab test |
|Case closure |	Incident closed as a true positive with benign authorized activity |

## Technical findings
### Process chain
```
powershell.exe (PID 11180)
└── powershell.exe (PID 7040)
    ├── -NoProfile
    └── -EncodedCommand <Base64 payload>
```
The parent and child executables were located at:
```
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```
### Encoded command
```
VwByAGkAdABlAC0ATwB1AHQAcAB1AHQAIAAnAFMATwBDAC0ATABBAEIALQBFAE4AQwBPAEQARQBEAC0AVABFAFMAVAAnAA==
```
The value was decoded as UTF-16LE and produced:
```powershell
Write-Output 'SOC-LAB-ENCODED-TEST'
```
The decoded command only printed a test string.

Executable hash
SHA256=8BB6FA8C283B4D92120B1EF249A9B311B0F804D4CABBE9981159976C8BE76A5E

The executable path and metadata were consistent with Windows PowerShell. A legitimate path or company field alone was not treated as proof of safety; the process context and decoded command were also examined.

Detailed technical evidence is available in:

Investigation 01: Encoded PowerShell Execution

## Scope and impact

The investigation was limited to the `WIN11-SOC` endpoint and the `PC_1` user account.

The following impact assessment was completed:

|Impact area |	Finding |
|---|---|
|Malicious code execution |	Not observed |
|File downloads |	Not observed |
|Persistence |	Not observed |
|Credential access |	Not observed |
|Security-control changes |	Not observed |
|Unexpected child processes |	Not observed |
|Unauthorized network activity |	Not observed |
|Data loss |	Not observed |
|Account compromise |	Not observed |
|Endpoint compromise |	Not observed |

No business or operational impact occurred.

## Root cause

The alert was caused by an authorized lab exercise in which the analyst deliberately executed a harmless Base64-encoded PowerShell command.

The security controls operated as intended:

- Sysmon captured the process creation.
- The Wazuh agent forwarded the telemetry.
- Wazuh identified the encoded PowerShell behavior.
- The alert received an appropriately high initial severity.
- Analyst triage established the benign context.

The event was not caused by a security-control failure or unauthorized user activity.

## Actions taken

The following response actions were completed:

1. Preserved the original Wazuh and Sysmon telemetry.
2. Reviewed the parent and child process relationship.
3. Examined the complete command line.
4. Decoded the Base64 payload without executing it.
5. Confirmed the user and endpoint involved.
6. Reviewed the payload for malicious functionality.
7. Checked for unexpected follow-on activity.
8. Classified the activity as an authorized lab test.
9. Closed the incident without containment or escalation.

## MITRE ATT&CK mapping
|Technique |	Name |	Tactic |
|---|---|---|
|`T1059.001` |	Command and Scripting Interpreter: PowerShell |	Execution |

The activity matched the behavioral characteristics of PowerShell execution. The ATT&CK mapping describes the observed technique and does not by itself establish malicious intent.

## Analyst assessment

The alert warranted investigation because it involved:

- PowerShell spawning another PowerShell process.
- The use of `-EncodedCommand`.
- Obfuscation of the command contents.
- A high-severity Wazuh alert.
- Behavior commonly associated with malicious scripts.

The activity was determined to be benign because:

- It was deliberately generated during an authorized exercise.
- The decoded payload contained only a harmless output command.
- The observed user and process chain matched the test.
- No malicious follow-on behavior was identified.
-No systems or accounts were compromised.

## Lessons learned

This incident demonstrated that high-severity alerts must be investigated in context rather than classified solely from their severity or ATT&CK mapping.

It also confirmed that the monitoring environment can:

- Collect Sysmon process-creation telemetry.
- Forward endpoint events to Wazuh.
- Detect encoded PowerShell execution.
- Preserve command-line and process-tree evidence.
- Support safe payload analysis.
- Distinguish malicious-looking behavior from authorized activity.

## Recommendations

For unexpected encoded PowerShell activity in a production environment:

1. Preserve the original process and command-line telemetry.
2. Decode the payload without executing it.
3. Validate the executable signature and hash.
4. Investigate the parent process and original execution method.
5. Search other endpoints for the same command, hash and user.
6. Review related network connections, file creation and child processes.
7. Check for persistence and security-control changes.
8. Isolate the endpoint if malicious activity is confirmed or strongly suspected.
9. Reset affected credentials if credential access is suspected.
10. Escalate according to the incident-response procedure.

## Final disposition
|Category |	Result |
|---|---|
|Detection |	Valid |
|Activity |	Authorized security test |
|Final severity |	Informational |
|Containment |	Not required |
|Recovery |	Not required |
|Escalation |	Not required |
|Case status |	Closed |
|Disposition |	True positive — benign authorized activity |

The incident was closed after confirming that the encoded command was harmless and that no endpoint or account compromise occurred.
