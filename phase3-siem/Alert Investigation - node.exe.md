## Alert Investigation node.exe Suspicious Execution

### Alert Details
| Field | Value |
|-------|-------|
| Timestamp | 2026-07-31 00:37:55 |
| Agent | RHEMA-MediaTech |
| Rule | Suspicious Windows cmd shell execution |
| MITRE Technique | T1087, T1059.003 |
| Severity | Level 3 |

### What Wazuh Detected
Wazuh detected node.exe being spawned by cmd.exe from a user directory. This pattern matches T1059.003 — Windows Command Shell execution, which is commonly used by attackers to run scripts via command prompt.

### Investigation Steps
1. Reviewed process relationship: `cmd.exe` spawned `node.exe` (matches the alert pattern for T1059.003).
2. Checked IntegrityLevel: Medium — process ran as a standard user, not elevated (High).
3. Checked currentDirectory: `C:\Users\rhema\Desktop\socials\` — user project path consistent with legitimate Node.js development.
4. Submitted file SHA256 to VirusTotal for confirmation. 

### VirusTotal Hash Check
- Hash: SHA256=9A4EB5F1C29C6A2E93852EAD46B999E284A6A5CA8BAB4D4E241D587D025A52DE
- Result: 0/70 vendors flagged as malicious — Clean
- Link: https://www.virustotal.com/gui/file/9A4EB5F1C29C6A2E93852EAD46B999E284A6A5CA8BAB4D4E241D587D025A52DE

### Triage Decision
- **Decision:** False Positive
- **Reasoning:** IntegrityLevel was Medium (not elevated), parent-child was cmd.exe → node.exe from C:\Users\rhema\Desktop\socials\ (expected for a local Node project), and the SHA256 hash returned 0/70 malicious detections on VirusTotal. Combined, this is a false positive, not malicious command execution.

### What This Taught Me
- Alert context alone is not enough. A complete triage decision requires process integrity, parent-child relationship, path legitimacy, and an independent hash verdict. 
