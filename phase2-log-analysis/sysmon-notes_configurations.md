## Sysmon Installation and Configuration — SOC Analyst Notes

## What is Sysmon?
**Sysmon** (System Monitor) is a Windows system service and device driver that, once installed, remains resident across system reboots to **monitor and log system activity** to the Windows event log. It provides detailed information regarding process creations, network connections, and changes to file creation times.

## Why SOC Analysts Use It
SOC analysts use Sysmon to gain deep visibility into endpoint activity, which is crucial for **identifying and investigating potential compromises**. By having Sysmon logs, analysts can track malicious behavior that standard Windows event logs might miss, such as specific network connection details.

## Installation Components I Learned

### Sysmon Binaries
When Sysmon is downloaded and extracted, it typically contains **three different binaries** (including `sysmon64.exe` for 64-bit systems). These are the executables used to install and manage the service.

### Configuration Files (.xml)
Sysmon requires a configuration file to define what activities should be logged. A popular choice among analysts is **Olaf's Sysmon configuration**, available on GitHub, which provides a comprehensive set of rules for detecting suspicious activity. During installation, this `.xml` file is passed as an argument to the binary.

## Sysmon Commands I Practiced

| Command | What it does |
|--------|--------------|
| `sysmon64.exe -i config.xml` | **Installs** the Sysmon service and driver using the specified configuration file. |
| `sysmon64.exe -c config.xml` | **Updates** the configuration of an existing Sysmon installation. |
| `sysmon64.exe -u` | **Uninstalls** the Sysmon service and driver. |
| `sysmon64.exe -s` | **Prints** the current Sysmon configuration to the console. |

## What a SOC Analyst Sees
- A running service named **"Sysmon64"** in the Windows Services manager.
- A new log directory in **Event Viewer** located under: `Applications and Services Logs > Microsoft > Windows > Sysmon > Operational`.
- Specific **Event IDs** for different activities; for example, **Event ID 3** specifically captures **network connections**.
- Detailed event manifests that help map out what occurred during a security incident.

## Tools to Configure and Investigate
| Tool | URL | What it shows |
|------|-----|---------------|
| Microsoft Learn | learn.microsoft.com | Provides official documentation and the **Event ID** reference list. |
| Olaf's GitHub | github.com (Olaf Hartong) | Provides a highly regarded **configuration file** for fine-tuning Sysmon logging. |
| Event Viewer | (Built-in Windows Tool) | Used to view and filter the **Sysmon logs** generated on the machine. |

## Key Takeaways
- **Verification is critical:** After installation, always check both the Windows Services manager and the Event Viewer to confirm that the `Sysmon64` service is running and generating logs.
- **Event ID Knowledge:** Understanding what specific IDs represent (like **ID 3 for network connections**) allows an analyst to quickly filter through data during an investigation.
- **Customization:** Using a community-standard configuration like Olaf’s ensures that you are capturing high-fidelity data rather than overwhelming your logs with "noise".
- **Practical Deployment:** Successfully installing and configuring Sysmon on a Windows Server is a foundational step in building a functional SOC environment.
