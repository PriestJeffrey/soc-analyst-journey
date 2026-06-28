## Sysmon Fundamentals — SOC Analyst Notes

## What is Sysmon?
Sysmon (System Monitor) is a free Microsoft tool from the **Sysinternals suite**. 
It is a system service and device driver that provides **advanced telemetry** by monitoring system activity and logging events to the Windows event log. 

## Why Analysts Use It
Analysts use Sysmon to achieve **endpoint visibility** that is missing from default Windows logging. While Windows logs some activity by default, 
it does not track critical events like **process creations**. 
Sysmon provides the deep data necessary to determine what occurred during a potential compromise, significantly increasing the probability of "catching evil".

## Key Capabilities I Learned

### Event Correlation (Process GUID)
The **Process GUID** (Globally Unique Identifier) is a unique field that allows analysts to **correlate related events** across different event IDs. 
This is essential for understanding the "bigger picture," such as linking a suspicious network connection back to the specific process and command line that initiated it.

### Detailed Forensics
Sysmon captures the **full command line** for both parent and current processes, which is vital for identifying malicious scripts or commands. 
It also records **file hashes**, enabling analysts to use Open-Source Intelligence (OSINT) to look up a file's reputation and gain additional context.

## Critical Sysmon Event IDs

| Event ID | What it does |
|----------|--------------|
| **Event ID 1** | **Process Creation:** Tracks new processes, command lines, and file hashes. |
| **Event ID 3** | **Network Connection:** Logs source/destination IPs and ports. **Disabled by default**. |
| **Event ID 10** | **Process Access:** Monitors attempts to access other processes, specifically targeting `lsass.exe` for credential theft. |
| **Event ID 22** | **DNS Query:** Records domain requests; useful for spotting **DGA (Domain Generation Algorithm)** activity. |
| **Event IDs 6, 7, 8** | **Advanced Injection:** Tracks driver/image loads and remote threads to identify **process injection** and defense evasion. |

## What a SOC Analyst Sees
- **Execution Chains:** An "odd binary" running from a temporary directory (ID 1) that then initiates a network connection (ID 3).
- **Credential Access:** Attackers attempting to tamper with or read the memory of the **Local Security Authority Subsystem Service (lsass.exe)** (ID 10).
- **C2 Activity:** Outbound connections to external IPs on non-standard ports or suspicious DNS queries to DGA domains (ID 22).
- **Evasion Techniques:** Malicious code being injected into legitimate processes to bypass antivirus or EDR detections (IDs 6, 7, 8).

## Tools to Investigate Activity
| Tool / Feature | Purpose |
|----------------|---------|
| **OSINT (e.g., VirusTotal)** | Used to check the **file hashes** captured in Sysmon logs for known malicious reputations. |
| **Configuration File** | Used to **customise and control** which events are logged, as some (like ID 3 and ID 7) are disabled by default. |

## Key Takeaways
- Understanding the **Process GUID** is the most effective way for an analyst to reconstruct the full chain of events during an investigation.
- Sysmon is **highly customizable**; using a configuration file is recommended to reduce "noise" and ensure critical events like network connections are being captured.
- Identifying anomalies such as **credential access attempts** or **DGA domain queries** allows analysts to catch compromised endpoints before an attacker moves laterally.
