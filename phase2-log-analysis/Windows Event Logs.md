## Windows Event Logs 
Windows Event Logs record everything happening on a Windows machine. They live in a built-in tool called Event Viewer.

## The three main log categories:
| Log | What it captures |
| Security | Logins, failed logins, privilege use, process creation, policy changes ApplicationSoftware errors, crashes, application-specific eventsSystemService starts/stops, reboots, driver issues, Windows updates
The Security Log is your primary log as a SOC analyst.
Critical Event IDs to know:
Event IDWhat it meansWhy it matters4624Successful logonWho logged in, when, from where, what type4625Failed logonBrute force indicator — look for repeated failures4688New process createdCatches commands run by attackers4720New user account createdPersistence technique — attackers create backdoor accounts1102Security log clearedMajor red flag. Attackers clear logs to cover tracks
Logon Types — inside Event ID 4624:
Logon TypeWhat it means2Interactive — someone physically at the machine3Network — accessing a shared resource remotely10RemoteInteractive — RDP session
When investigating a suspicious login, answer these four questions from the log:

What happened?
When did it happen?
Who did it?
Where did it come from?
