## Sysmon 
Default Windows logs miss a lot. Sysmon fills the gaps.
Sysmon is a free Microsoft tool from the Sysinternals suite. Once installed, it runs as a service and logs deep endpoint activity to Event Viewer under:
Applications and Services Logs > Microsoft > Windows > Sysmon > Operational

| Event ID | What It Captures | Why It Matters |
|----------|------------------|----------------|
| 1        | Process creation (full command line, file hash, parent process) | Shows exactly what was executed and how |
| 3        | Network connection (source/destination IPs and ports) | Detects beaconing and suspicious outbound traffic |
| 10       | Process access (e.g., access to lsass.exe) | Can indicate credential dumping attempts |
| 17       | Pipe created (named pipe creation) | Often used by malware for inter-process communication |
| 22       | DNS query logs | Helps detect DGA domains and C2 communication |

The Process GUID: Every process gets a unique ID. This lets you link related events across different Event IDs. 
Example: you see a suspicious network connection in Event ID 3; use the Process GUID to trace it back to the exact command that launched it in Event ID 1.

4. Parent-Child Process Relationships
Every process on Windows has a parent... the process that launched it.
Why this matters: Attackers use legitimate programs to run malicious commands. By looking at the parent-child relationship, you catch it.

| Field            | What it Tells You |
|------------------|------------------|
| Image            | The process that ran |
| CommandLine      | Exact command executed |
| ParentImage      | Process that launched it |
| ParentCommandLine| Command used by the parent |
| User             | Logged-in user |
| IntegrityLevel   | Privilege level of the process |

IntegrityLevel explained:
| Level   | Meaning |
|---------|--------|
| Low     | Restricted / sandboxed process |
| Medium  | Standard user-level applications |
| High    | Administrator privileges |
| System  | Core OS-level processes |

High integrity running unexpected commands = an attacker has admin access. That's serious.

## MITRE ATT&CK Mapping

Sysmon with Olaf's configuration automatically tags events with MITRE ATT&CK technique IDs. 
This means your logs don't just tell you what happened, they tell you what attack technique was used.

Techniques I encountered in my own logs:
| Command         | MITRE ID | Technique Name                      |
|-----------------|----------|-------------------------------------|
| whoami          | T1033    | System Owner/User Discovery         |
| net user        | T1033    | System Owner/User Discovery         |
| net localgroup administrators | T1069.001 | Permission Groups Discovery |

## Why attackers run these commands: 
They're reconnaissance. An attacker who just got into a machine runs these to figure out who they are, what privileges they have, and what accounts exist. 
They're mapping the environment before moving further.

## How to Write a Sysmon Finding
Every finding in an incident report must answer: what, when, who, how, and why it matters.

Template:

On [date] at [time], user [username] on machine [hostname] executed [Image/child process] via [ParentImage/parent process] with [IntegrityLevel] privileges. 
The command [CommandLine] maps to MITRE ATT&CK technique [ID] — [Technique Name]. This indicates [what the attacker was doing and why it's a risk].

## My real finding from today's session:

On 2026-06-29 at 22:52:40, user RHEMA-MediaTech\rhema on machine RHEMA-MediaTech executed net.exe via C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe with High integrity privileges. 
The command "net user" maps to MITRE ATT&CK technique T1033 System Owner/User Discovery. 
This indicates the attacker had administrator-level access and was enumerating user accounts on the machine, likely to identify privileged accounts for further exploitation.

## Key Takeaways From This Session

- Default Windows logs miss process creation details. Sysmon fills that gap.
- Parent-child relationships reveal how an attacker moved through the system. Always ask: what launched this process and why?
- IntegrityLevel: High means the process had admin rights. That raises the severity of any suspicious finding.
- Sysmon with Olaf's config maps events to MITRE ATT&CK automatically. Use those technique IDs in every report.
- Precision matters. Wrong field in a report = miscommunication during a live incident.
