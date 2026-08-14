## Alert Investigation — pool_tags_summary.json.dup (Sysmon EID11)
### Alert Details
| Field | Value |
|-------|-------|
| Timestamp | 2026-08-13 21:32:35 |
| Agent | RHEMA-MediaTech |
| Rule | Scripting file created under Windows Temp or User folder |
| Rule ID | 92200 |
| MITRE Technique | T1059, T1105 |
| Severity | Level 6 |
| Sysmon event ID | 11 |

### What Wazuh Detected
Wazuh Event showed Scripting file created under Windows Temp or User folder. Rule 92200 mapped this to T1059 ( Command and Scripting Interpreter ) and T1105 ( Ingress Tool Transfer ) because a script-like file was created under Temp or a user folder. That can be malware staging or normal app/installer behavior.

### Investigation Steps
1. Found out the image "C:\\\\windows\\\\system32\\\\svchost.exe"  and what the target filename "C:\\\\Windows\\\\Temp\\\\pool_tags_summary.json.dup" were.
2. Checked target path C:\Windows\Temp\pool_tags_summary.json.dup, CreationUtcTime 2026-08-13 21:32:34.846, User NT AUTHORITY\SYSTEM.
3. Noted rule.firedtimes = 10 
4. Hashed svchost.exe and checked VirusTotal.

### Key Evidence
| Check | Finding |
| -------- | -------- |
|  image | C:\windows\system32\svchost.exe |
| TargetFilename | C:\Windows\Temp\pool_tags_summary.json.dup |
| Rule.firedtimes | 10 |
| VT ( svchost ) | Clean - 1222A44A5FDB4EFDE4DFCB41093648627950E7EC02D8667F1C26CCAE31D922E2 |
| User | NT AUTHORITY\SYSTEM |

### VirusTotal Hash Check
- Hash: SHA256=1222A44A5FDB4EFDE4DFCB41093648627950E7EC02D8667F1C26CCAE31D922E2
- File hashed: C:\Windows\System32\svchost.exe (target Temp file not present at first check)
- Result: Clean
- Link: https://www.virustotal.com/gui/file/1222a44a5fdb4efde4dfcb41093648627950e7ec02d8667f1c26ccae31d922e2
 
### Triage Decision

- **Decision:** False Positive
- **Reasoning:** 
this alert is a false positive because, svchost is a host for Windows services. One of those services (running as SYSTEM) wrote a pool-tag memory summary JSON into C:\Windows\Temp. 
That’s diagnostic/data output not script staging and A pool tag is a 4-character ASCII identifier that Windows uses to track kernel-mode memory allocations. 
both paths "C:\\\\windows\\\\system32\\\\svchost.exe", "C:\\\\Windows\\\\Temp\\\\pool_tags_summary.json.dup" are legitimate windows locations.  

### What This Taught Me
T1059/T1105 labels are not proof. Confirm process path ( C:\Windows\System32\svchost.exe ), file type ( JSON data/summary ), and hash before deciding ( VT clean for that svchost.exe ).

## False Positive Tuning Notes

### Why the Rule Fired
This rule fired because a file was created under Temp/User and matched a scripting/staging-style pattern.

### Why This Was a False Positive
this alert is a false positive because, svchost is a host for Windows services. One of those services (running as SYSTEM) wrote a pool-tag memory summary JSON into C:\Windows\Temp. 
That’s diagnostic/data output not script staging and A pool tag is a 4-character ASCII identifier that Windows uses to track kernel-mode memory allocations. 
both paths "C:\\\\windows\\\\system32\\\\svchost.exe", "C:\\\\Windows\\\\Temp\\\\pool_tags_summary.json.dup" are legitimate windows locations. 

### Tuning Idea
| Item | Detail |
|------|--------|
| Tuning type | Exclusion / allowlist |
| Condition |	Exclude when Image = C:\Windows\System32\svchost.exe AND TargetFilename = C:\Windows\Temp\pool_tags_summary.json.dup (or matches *\pool_tags_summary.json*) |
| Risk if mistuned | If broadened to all Temp creates by svchost, real malicious drops from a compromised/spoofed host process could be missed |
| Safer alternative |Scope only to signed/known svchost.exe path do not exclude all pool_tags |

### What I Would Not Do
- I would not allowlist all svchost.exe activity or all pool_tags, because that can hide real script files created by a malware.
