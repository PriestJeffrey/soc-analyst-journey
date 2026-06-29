## Windows Event Logs 
Windows Event Logs record everything happening on a Windows machine. They live in a built-in tool called Event Viewer.

## The three main log categories:
| Log Type    | What it Captures                                                                 |
|--------------|----------------------------------------------------------------------------------|
| Security     | Logins, failed logins, privilege use, process creation, policy changes          |
| Application  | Software errors, crashes, application-specific events                           |
| System       | Service starts/stops, reboots, driver issues, Windows updates                   |

The Security Log is your primary log as a SOC analyst.

## Critical Event IDs to know:
| Event ID | What it Means           | Why it Matters                                                     |
|----------|--------------------------|--------------------------------------------------------------------|
| 4624     | Successful logon         | Shows who logged in, when, from where, and the logon type.        |
| 4625     | Failed logon             | Can indicate a brute-force attack if there are repeated failures. |
| 4688     | New process created      | Helps detect commands or programs executed by attackers.          |
| 4720     | New user account created | May indicate persistence through a backdoor account.              |
| 1102     | Security log cleared     | A major red flag—attackers often clear logs to hide their tracks. |

| Logon Type | What it Means                                      |
|------------|----------------------------------------------------|
| 2          | Interactive — someone physically at the machine    |
| 3          | Network — accessing a shared resource remotely     |
| 10         | RemoteInteractive — RDP session                    |

## When investigating a suspicious login, answer these four questions from the log:
What happened?
When did it happen?
Who did it?
Where did it come from?
