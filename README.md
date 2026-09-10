# SOC analyst home lab

Adjei Jeffery Amoafo. Accra, Ghana.  
NOC / infrastructure (AirtelTigo, Jan 2023 to Jan 2025). Targeting SOC Analyst (L1).

This repo is the lab: SIEM deploy, alert triage (false positive vs true positive), and custom detections.  
LinkedIn: [jeffery-adjei-0b38802a2](https://www.linkedin.com/in/jeffery-adjei-0b38802a2)

## What this repo proves

- Wazuh (Docker) + Windows agent + Sysmon. Alerts triaged and documented, not just screenshots.
- Custom Wazuh rules for discovery commands (cmd spawning whoami, ipconfig, tasklist) and false-positive exclusions. Rules: `phase3-siem/local_rules.xml`.
- Packet and Windows log investigations (pcap, Sysmon, PsExec hunt).
- Splunk search practice, including pivoting one IP across auth and web logs. Intro lab, not production SOC Splunk.

## Start here (Wazuh)

1. [Wazuh deployment](phase3-siem/wazuh-deployment.md)
2. [Alert investigation: node.exe (FP)](phase3-siem/Alert%20Investigation%20-%20node.exe.md)
3. [Custom detection: cmd.exe spawned whoami.exe (T1033)](phase3-siem/wazuh-custom-detection-whoami.md)
4. [Detection drills](phase3-siem/wazuh-detection-drills.md) and [manager commands](phase3-siem/wazuh-commands.md)

## Wazuh (SIEM)

- [Detection tuning](phase3-siem/wazuh-detection-tuning.md)
- [Cursor.exe to Explorer (T1055 FP)](phase3-siem/Alert%20Investigation%20-%20cursor-explorer.md)
- [MoUsoCoreWorker.exe loaded taskschd.dll (T1053.005 FP)](phase3-siem/Alert%20Investigation%20-%20mousocoreworker-taskschd.md)
- [pool_tags_summary.json.dup (Sysmon EID 11)](phase3-siem/Alert%20Investigation%20-%20pool-tags-summary.md)
- [local_rules.xml](phase3-siem/local_rules.xml)

## Splunk

- [Intro search notes](phase4-splunk/splunk-intro-notes.md) (in progress)

## Logs and packets

- [Windows event logs](phase2-log-analysis/windows-event-logs.md)
- [Sysmon notes](phase2-log-analysis/sysmon-notes.md)
- [PsExec hunt](phase2-log-analysis/PsExec%20Hunt.md)
- [Tomcat takeover](phase2-log-analysis/Tomcat%20Takeover.md)
- [Web investigation](phase2-log-analysis/Web%20investigation.md)
- [pcap investigation 01](phase1-networking/pcap-investigation-01.md)
- [pcap investigation 02](phase1-networking/pcap-investigation-02.md)
- [pcap investigation 03](phase1-networking/pcap-investigation-03.md)
- [Nmap reconnaissance](phase1-networking/nmap-reconnaissance.md)
- [Wireshark basics](phase1-networking/wireshark-basics.md)

## Certifications

- Cisco CyberOps Associate
- Cisco CCNA: Introduction to Networks (not full CCNA)
- Microsoft Azure Fundamentals (AZ-900)
