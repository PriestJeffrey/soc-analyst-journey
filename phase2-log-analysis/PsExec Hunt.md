## Incident Report — PsExec Hunt

## Scenario
An IDS alert flagged suspicious lateral movement activity on the network. 
The task was to analyze a PCAP file to identify how the attacker moved 
laterally using PsExec, what machines were compromised, and what credentials 
or shares were abused during the attack.

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
because it was there was a suspicion of lateral movement activity on the network.
2. So i decided to look at the machine that was compromised by the machine and i saw that the victim was receiving a lot of
traffic. the victim's machine was 10.0.0.133 and the attacker's machine was 10.0.0.130. I got this by chcecking for conversations on the statistic tab.
3. i filtered for SMB traffic using the filter smb2. i didn't fully understand by i was supposed to use the smb2 traffic then i found out that, there are two major versions of SMB that ie; SMB1 which is old, largely disabled on modern Windows, and infamous for the EternalBlue exploit used in WannaCry and there is SMB2/SMB3 which is the modern versions used by Windows 7 and above.
4. the PsExec on modern windows uses SMB2.
5. i found out in the conversation tab that the attacker was listening on port 445 and that is the standard SMB port.
6. i filtered the traffic with this smb2 and ip.addr == 10.0.0.130 . and i found out these; 10.0.0.133 wrote a file called PSEXESVC.exe to \\10.0.0.133\ADMIN$ and 10.0.0.130 created a request to \\10.0.0.133\IPC$.
7. this means that, the attacker copied files onto the victim's machine.
8.  Wrote a structured finding for each event.

## Findings
On Oct 11, 2023 at 07:42:08 UTC, source IP 10.0.0.130 initiated an SMB2 connection to destination IP 10.0.0.133 on port 445. PSEXESVC.exe was written to \10.0.0.133\ADMIN$ via a Write Request over SMB2. This maps to MITRE ATT&CK technique T1021.002 - Remote Services: SMB/Windows Admin Shares. This indicates the attacker copied the PsExec service binary onto the victim machine to enable remote code execution.

## MITRE ATT&CK Techniques Identified
| Activity | Technique ID | Technique Name |
|----------|-------------|----------------|
| Lateral Movement |T1021.002  | Remote Services: SMB/Windows Admin Shares |

## Indicators of Compromise (IOCs)
- Unusual amount of traffic flowing between these two IP addresses; 10.0.0.130 and 10.0.0.133
- 

## Conclusion

In this lab, i was finding out the lateral movement of an attacker and what machines it compromised.


## What This Taught Me About SOC Work
- when you see a lot of traffic coming from a a specific address, it must create suspicion.
- attackers will normally like to hide their operations using legitimate tools in the operating system. 
- 
