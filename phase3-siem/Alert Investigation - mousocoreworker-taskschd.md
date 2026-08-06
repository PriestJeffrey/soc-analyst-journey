## Alert Investigation — MoUsoCoreWorker.exe loaded taskschd.dll

### Alert Details
| Field | Value |
|-------|-------|
| Timestamp | 2026-08-06 00:40:04 |
| Agent | RHEMA-MediaTech |
| Rule | Process loaded taskschd.dll |
| Rule ID | 92154 |
| MITRE Technique | T1053.005 |
| Severity | Level 4 |

### What Wazuh Detected
Wazuh detected a process loading taskschd.dll. Rule 92154 mapped this to T1053.005 (Scheduled Task) because loading Task Scheduler components can indicate delayed or persistent execution.

### Investigation Steps
1. Found out Process (image) C:\Windows\UUS\amd64\MoUsoCoreWorker.exe Module (imageLoaded) C:\Windows\System32\taskschd.dll. MoUsoCoreWorker.exe opened taskschd.dll to run a task.
2. Looked for the full path C:\Windows\UUS\amd64\MoUsoCoreWorker.exe, C:\Windows\System32\taskschd.dll and they were not suspicious
3. Noted rule.firedtimes = 10 .
4. Hashed MoUsoCoreWorker.exe and checked VirusTotal.

### Key Evidence
| Check | Finding |
| -------- | -------- |
| Process (image) | C:\Windows\UUS\amd64\MoUsoCoreWorker.exe |
| Module (imageLoaded) | C:\Windows\System32\taskschd.dll |
| VT | Clean - SHA256 1E3292A4C58708CA4E3A73B82DB0E9CA4A2C1FC640526B9A14337A1A55A36AA7 |
| Rule.firedtimes | 10 |


### VirusTotal Hash Check
- Hash: SHA256 1E3292A4C58708CA4E3A73B82DB0E9CA4A2C1FC640526B9A14337A1A55A36AA7
- Result: 0 detections across listed vendors — Clean
- Link: https://www.virustotal.com/gui/file/1e3292a4c58708ca4e3a73b82db0e9ca4a2c1fc640526b9a14337a1a55a36aa7/detection

### Triage Decision

- **Decision:** False Positive
- **Reasoning:** 
this alert is a false positive because, the MoUsoCoreWorker.exe (Microsoft Update Session Orchestrator) is a legitimate Windows system process responsible for managing and checking background system updates in Windows 10 and Windows 11 and the taskschd.dll is the Windows library used to work with Task Scheduler. both paths C:\Windows\UUS\amd64\MoUsoCoreWorker.exe, C:\Windows\System32\taskschd.dll are legitimate windows locations. the hash files SHA256 1E3292A4C58708CA4E3A73B82DB0E9CA4A2C1FC640526B9A14337A1A55A36AA7 appeared cleaned at virustotal https://www.virustotal.com/gui/file/1e3292a4c58708ca4e3a73b82db0e9ca4a2c1fc640526b9a14337a1a55a36aa7/detection.
Windows Update uses Task Scheduler, so MoUso loading taskschd.dll is expected.

### What This Taught Me
A T1053.005 label is not proof. Identify the process, verify Windows paths for both image and module, and confirm the process hash before deciding.

## False Positive Tuning Notes

### Why the Rule Fired
the rule fired because it suspected process loaded taskschd.dll module which may be used to create delayed malware execution. 

### Why This Was a False Positive
this alert is a false positive because, the MoUsoCoreWorker.exe (Microsoft Update Session Orchestrator) is a legitimate Windows system process responsible for managing and checking background system updates in Windows 10 and Windows 11 and the taskschd.dll is the Windows library used to work with Task Scheduler. both paths C:\Windows\UUS\amd64\MoUsoCoreWorker.exe, C:\Windows\System32\taskschd.dll are legitimate windows locations. the hash files SHA256 1E3292A4C58708CA4E3A73B82DB0E9CA4A2C1FC640526B9A14337A1A55A36AA7 appeared cleaned at virustotal https://www.virustotal.com/gui/file/1e3292a4c58708ca4e3a73b82db0e9ca4a2c1fc640526b9a14337a1a55a36aa7/detection

### Tuning Idea
| Item | Detail |
|------|--------|
| Tuning type | Exclusion / allowlist |
| Condition |	exclude when image is ...\MoUsoCoreWorker.exe under C:\Windows\UUS\ AND imageLoaded is C:\Windows\System32\taskschd.dll - not all taskschd.dll loads |
| Risk if mistuned | If you exclude all access to taskschd.dll, taskschd.dll module may be used to create delayed malware execution |
| Safer alternative | Scope only to signed/known Process (image) C:\Windows\UUS\amd64\MoUsoCoreWorker.exe and Module (imageLoaded) C:\Windows\System32\taskschd.dll + do not exclude all taskschd.dll events |

### What I Would Not Do
- I would not allowlist all taskschd.dll  events, because that can  be used to create delayed malware execution.
