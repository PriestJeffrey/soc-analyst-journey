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
1. wazuh flagged node.exe running from cmd.exe as suspicious. but these were actually part of the project i was running. 
2. the alert had 3 fields that were worth noting;
  - IntegrityLevel: Medium. Medium means it's running as a normal user, not elevated.
    That's actually a good sign because a malware trying to do serious damage usually runs at High integrity.
  - currentDirectory: C:\Users\rhema\Desktop\socials\; this just a normal folder on my desktop and it has nothing to do with security.
3. SHA256=9A4EB5F1C29C6A2E93852EAD46B999E284A6A5CA8BAB4D4E241D587D025A52DE; this was a hashed file in the project and it was checked with virustotal.com
and it appeared to be clean and not malicious.  

### VirusTotal Hash Check
- Hash: SHA256=9A4EB5F1C29C6A2E93852EAD46B999E284A6A5CA8BAB4D4E241D587D025A52DE
- Result: Result: 0/70 vendors flagged as malicious - Clean
- Link: https://virustotal.com

### Triage Decision
- **Decision:** False Positive
Wazuh triggered node.exe and cmd.exe even though it they were not malicious even. the hash file was verified
at virus total and it appeared to be clean. 

### What This Taught Me
- A soc analyst must go beyond context reasoning. A soc analyst must go further and check if files are malicious or not. 
