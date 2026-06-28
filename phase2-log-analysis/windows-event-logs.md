# Windows Event Log Analysis — SOC Analyst Notes

## What are Windows Event Logs?
Windows event logs are records of activity happening behind the scenes on a Windows operating system. 
They capture a wide range of events, including logins and logoffs, process creations, service starts and stops, errors, and system warnings. 
These logs are centrally organized and stored within a built-in Windows tool called **Event Viewer**.

## Why SOC Analysts Use Them
Understanding these logs is considered a foundational skill that makes analysts confident in their analysis and more impressive during job interviews. 
While Security Information and Event Management (SIEM) tools often provide summaries, SOC analysts must often dig into the **raw events** to confirm suspicious activity or find context that automation might have missed. 
Mastering these logs allows an analyst to validate alerts, dismiss false positives, and identify threats that other analysts might overlook.

## Main Log Categories
### Security Log
The security log is the most critical category for a SOC analyst. 
It tracks authentication details, such as who logged in and when, whether the login was successful, and what "type" of login occurred. 
It also records object access, privilege use, policy changes, and process creations if those features are enabled.

### Application and System Logs
The **Application log** captures events from software running on the system, such as application crashes or specific errors. 
The **System log** captures events related to the operating system itself, including driver issues, system reboots, and the starting or stopping of services.

## Event IDs and Fields I Practiced
| ID or Field | What it shows |
| :--- | :--- |
| **Event ID 4624** | Indicates a successful logon occurred. |
| **Event ID 4625** | Failed logon attempt |
| **EVent ID 4688** | New process created catches commands run by attackers |
| **Event ID 4720** | New user account created |
| **Event ID 1102** | Security audit log cleared a major red flag, attackers do this to cover tracks |
| **Logon Type 10** | Specific field indicating a Remote Desktop (RDP) logon. |
| **Logon Type 3** | Specific field indicating a network-based logon. |
| **Source IP Address** | The network address from which the connection originated. |
| **User Account** | The specific account used to perform the action. |

## What a SOC Analyst Sees
When investigating an alert, such as a suspicious login, the analyst looks for the specific **Event ID** 
and then analyzes the fields within that single event to answer core questions: what happened, when it happened, who did it, and where it came from. 
For example, the analyst can determine if a session was a brand-new login or a reused session and identify the specific source IP and user account involved.

## Additional Logs for Deep Visibility
| Log / Tool | What it shows |
| :--- | :--- |
| **Terminal Services** | Specifically tracks Remote Desktop (RDP) activity. |
| **Task Scheduler** | Shows the creation of scheduled tasks, often used by attackers for persistence. |
| **PowerShell Logs** | Records the actual commands executed via PowerShell if script block and module logging are enabled. |
| **Sysmon** | An additional layer that provides deep visibility into network connections, file changes, and process creations. |

The **Sysymon** matters more in a SOC environment because it fills the critical visibility gaps left by 
default windows event logs.

## Key Takeaways
*   **Focus on Structure over Memorization:** Instead of trying to memorize every event ID, focus on understanding the structure of the logs to identify who, what, where, and when an event occurred.
*   **Go Beyond the "Big Three":** While the Security, Application, and System logs are the starting point, valuable forensic data is often hidden in specialized logs like PowerShell or Task Scheduler.
*   **Explain the "How" in Interviews:** When asked about investigations, show expertise by walking through specific event IDs, fields, and the actual steps taken rather than just speaking in general theories.
