## Incident Report - Tomcat Takeover

## Scenario
A suspicious activity was identified on a web server within the company's intranet. 
The task was to analyze a PCAP file to understand how the Apache Tomcat web server was compromised
and to the malicious activities that led to the compromise of the Apache Tomcat web server.

- File type: PCAP
- Protocol of interest: SMB2
- Tool used by attacker: PsExec
- Investigation focus: Lateral movement, admin shares, NTLM authentication

## Tools Used
- Wireshark
- VirusTotal
- MITRE ATT&CK Navigator

## Investigation Steps
1. Opened a PCAP file gotten from cyberdefenders to analyze a an IDS alert that was flagged
because there was a suspicion activity that was identified on a web server.
2. Navigated to statistics, then to conversation to identify top talkers and to determine attacker and victim IPs
3. Filtered the traffic with this ip.addr==14.0.0.120.
4. Looked at the traffic and found that the protocols that were being used were TCP protocols running on source port; 256 and destination port 51985.
5. Filtered the traffic with this http and ip.addr == 14.0.0.120
6. Wrote a structured finding for each event.

## Findings
1. On Oct 11, 2023 at 07:42:08 UTC, source IP 10.0.0.130 initiated an SMB2 connection to destination IP 10.0.0.133 on port 445. PSEXESVC.exe was written to \10.0.0.133\ADMIN$ via a Write Request over SMB2. This maps to MITRE ATT&CK technique T1021.002 - Remote Services: SMB/Windows Admin Shares. This indicates the attacker copied the PsExec service binary onto the victim machine to enable remote code execution.
 ![psexesvc_Write Response packet](screenshots/psexesvc-write-response.png)     ![top-talker](screenshots/top-talker.png)

2. On Oct 11, 2023 07:46, source IP 10.0.0.130 Attacker attempted to authenticate using account IEUser on 10.0.0.131 (HR-PC) on port 49703 It resulted in an access denial; STATUS_ACCESS_DENIED - authentication failed This maps to MITRE ATT&CK technique T1021.002 — Remote Services: SMB/Windows Admin Shares. This indicates that, the attacker wanted to compromise the HR-PC after compromising the SALES-PC
![STATUS_ACCESS_DENIED HR-PC](screenshots/status-access-denied.png)
## MITRE ATT&CK Techniques Identified
| Activity | Technique ID | Technique Name |
|----------|-------------|----------------|
| Lateral Movement |T1021.002  | Remote Services: SMB/Windows Admin Shares |
| Stolen credentials | T1078| Valid Accounts |

## Indicators of Compromise (IOCs)
- Port 256 which is an unusual port for a web traffic.
- Attacker IP: 14.0.0.120
- Victim IP: 10.0.0.112


## Conclusion

The attacker first compromised the Sales-PC using the technique of lateral movement. The attacker used PsExec over SMB2 to move laterally and installed PSEXESVC.exe. The attacker copied the PsExec service binary onto the victim machine to enable remote code execution, then tried gaining access to the HR-PC but the authentication failed because of wrong credentials. the attacker was able to gain remote access to the victim's PC using the PsExec tool in windows


## What This Taught Me About SOC Work
- Seeing PsExec activity running from a SALES-PC rather than an IT admin machine is an indicator that the SALES-PC has been compromised. PsExec should only be run by administrators not end-user workstations. 
- attackers will normally like to hide their operations using legitimate tools in the operating system. 
- An IOC must be something that stands out as abnormal. 
