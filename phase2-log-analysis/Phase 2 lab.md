## Incident Report — Sysmon lab

## Scenario
This lab was conducted on a personal Windows machine running Sysmon 
with Olaf Hartong's configuration. The objective was to simulate 
attacker reconnaissance commands via PowerShell and identify the 
resulting events in Sysmon logs, mapped to MITRE ATT&CK techniques.

- Machine: RHEMA-MediaTech
- User: RHEMA-MediaTech\rhema
- OS: Windows 11
- Sysmon Config: Olaf Hartong (SwiftOnSecurity fork)
- Date: 30/06/2026

## Tools Used
- Powershell
- Windows Event viewer ( sysmon )

## Investigation Steps
1. Opened PowerShell as Administrator on machine RHEMA-MediaTech.
2. Executed the following reconnaissance commands:
   - net localgroup administrators
   - ipconfig
   - systeminfo
3. Opened Event Viewer and navigated to:
   Applications and Services Logs > Microsoft > Windows > Sysmon > Operational
4. Filtered for Event ID 1 (Process Creation) to locate the generated events.
5. Identified each command in the logs by matching the CommandLine field.
6. Extracted the following fields from each event:
   - Image, CommandLine, ParentImage, User, IntegrityLevel, UtcTime
7. Mapped each command to its corresponding MITRE ATT&CK technique.
8. Wrote a structured finding for each event.

## Findings

**Finding 1 — net localgroup administrators**
On 30/06/2026  at 12:10:58am, user RHEMA-MediaTech\rhema on machine RHEMA-MediaTech executed 
C:\Windows\System32\net.exe via C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe with 
IntegrityLevel: High privileges. The command "C:\windows\system32\net.exe" localgroup administrators maps to 
MITRE ATT&CK technique T1069.001- local groups  Permission Groups Discovery. 
This indicates that the attacker wanted to know the names of the admins which have the administrator privileges on the machine. 
this is so that the can target one of those accounts and see if they can steal credentials

**Finding 2 — ipconfig**
On 30/06/2026 at 12:11:10 am, user RHEMA-MediaTech\rhema on machine RHEMA-MediaTech executed 
C:\Windows\System32\ipconfig.exe via C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe with 
IntegrityLevel: High privileges. The command "C:\windows\system32\ipconfig.exe" maps to MITRE ATT&CK technique T1016 System Network Configuration discovery . 
This indicates that the attacker was trying to know the system networking configurations of the machine.

**Finding 3 — systeminfo**
On 30/06/2026 at 12:11:17 am, user RHEMA-MediaTech\rhema on machine RHEMA-MediaTech executed 
C:\Windows\System32\systeminfo.exe via C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe with 
IntegrityLevel: High privileges. The command "C:\windows\system32\systeminfo.exe" maps to MITRE ATT&CK technique T 1082 System information Discovery. 
This indicates that the attacker was trying to know details of the machine. details like name of the Host machine, OS name, OS version, product ID, BIOS Version... just to mention a few

## ## MITRE ATT&CK Techniques Identified
| Command | Technique ID | Technique Name |
|---------|-------------|----------------|
| net localgroup administrators | T1069.001 | Permission Groups Discovery |
| ipconfig | T1016 | System Network Configuration Discovery |
| systeminfo | T1082 | System Information Discovery | 

## Conclusion
[One paragraph. What happened, how, and what the attacker achieved.]

## What This Taught Me About SOC Work
- 
- 
-
