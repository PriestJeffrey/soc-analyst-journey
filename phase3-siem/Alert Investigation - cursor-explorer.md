## Alert Investigation - Cursor.exe accessing Explorer.EXE

### Alert Details
| Field | Value |
|-------|-------|
| Timestamp | 2026-08-04 02:08:26 |
| Agent | RHEMA-MediaTech |
| Rule | Explorer process was accessed by Cursor.exe, possible process injection |
| Rule ID | 92910 |
| MITRE Technique | T1055 |
| Severity | Level 12 |
| Sysmon Event ID | 10 |

### What Wazuh Detected
Wazuh/Sysmon Event ID 10 showed Cursor.exe accessing Explorer.EXE. Rule 92910 mapped this to T1055 (Process Injection) because process access to Explorer can indicate injection.

### Investigation Steps
1. Found out the source process ( C:\\Users\\rhema\\AppData\\Local\\Programs\\cursor\\Cursor.exe )  and what the target process ( C:\\windows\\Explorer.EXE ) were.
2. Looked for the full path cursor.exe was running from to see if it looked suspicious C:\\Users\\rhema\\AppData\\Local\\Programs\\cursor\\Cursor.exe but it didn't look suspicious.
3. Checked grantedAccess (`0x40`) — handle/dup permission, not classic memory-write injection rights.
4. Reviewed call stack — included `shcore.dll` and `explorerframe.dll` (Windows shell/UI).
5. Noted rule.firedtimes = 74 (noisy recurring behavior).
6. Hashed Cursor.exe and checked VirusTotal.

### Key Evidence
- Source ( Cursor.exe ) / Target ( Explorer.EXE )
- grantedAccess: 0x40
- Call stack highlight explorerframe.dll / shcore.dll
- firedtimes : 74 times
- Path legitimacy: C:\Users\rhema\AppData\Local\Programs\cursor\Cursor.exe

### VirusTotal Hash Check
- Hash: SHA256=4E369A189EF6660B85BF34F1F2934C381CC4FBF43A0D9EF31E60BE272811CFE1
- Result: 0 detections across listed vendors — Clean
- Link: https://www.virustotal.com/gui/file/4E369A189EF6660B85BF34F1F2934C381CC4FBF43A0D9EF31E60BE272811CFE1

### Triage Decision

- **Decision:** False Positive
- **Reasoning:** 
cursor asked windows for permission to touch explorer. when cursor touched explorer it didn't have the permission to write into explorer's memory; that will have been something to be suspicious about but it only had a different permission to use a duplicate handle that is 0x40 which is a normal app behaviour. 
when cursor touched explorer, there were traces of the UI stack that were modules in the access call stack breadcrumb trail shcore.dll, explorerframe.dll which are actually windows shell/UI components.
the alert fired 74 times but that's not suspicious because its just a noisy recurring behaviour while cursor was in use. to finally conclude whether it was malicious or not, a hash check was done on virusTotal 4E369A189EF6660B85BF34F1F2934C381CC4FBF43A0D9EF31E60BE272811CFE1 which appeared to be clean

### What This Taught Me
- High-severity T1055 labels are not proof. Validate access rights, call stack, frequency, path, and hash before deciding.
The SOC analyst must be able to look through the evidences to see if the alert is legitimate or malicious.

## False Positive Tuning Notes

### Why the Rule Fired
the rule fired because it suspected a process injection. this was because Explorer process was accessed by C:\\Users\\rhema\\AppData\\Local\\Programs\\cursor\\Cursor.exe, possible process injection

### Why This Was a False Positive
cursor asked windows for permission to touch explorer. when cursor touched explorer it didn't have the permission to write into explorer's memory; that will have been something to be suspicious about but it only had a different permission to use a duplicate handle that is 0x40 which is a normal app behaviour. 
when cursor touched explorer, there were traces of the UI stack that were modules in the access call stack breadcrumb trail shcore.dll, explorerframe.dll which are actually windows shell/UI components.
the alert fired 74 times but that's not suspicious because its just a noisy recurring behaviour while cursor was in use. to finally conclude whether it was malicious or not, a hash check was done on virusTotal 4E369A189EF6660B85BF34F1F2934C381CC4FBF43A0D9EF31E60BE272811CFE1 which appeared to be clean
### Tuning Idea
| Item | Detail |
|------|--------|
| Tuning type | Exclusion / allowlist |
| Condition |	Exclude when sourceImage is the official Cursor path (...\Programs\cursor\Cursor.exe) AND targetImage is Explorer.EXE AND grantedAccess is 0x40 |
| Risk if mistuned | If you exclude all access to Explorer, real injection/access abuse against Explorer can be missed |
| Safer alternative |Scope only to signed/known Cursor install path + this access right; do not exclude all Explorer process-access events |

### What I Would Not Do
- I would not allowlist all Cursor.exe activity or all Explorer process-access events, because that can hide real process injection or Explorer access abuse.
