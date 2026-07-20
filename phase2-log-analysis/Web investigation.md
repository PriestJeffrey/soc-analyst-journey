## Incident Report - Web Investigation

## Scenario
A suspicious activity was identified on a web server within the company's intranet. 
The task was to analyze a PCAP file to understand the attack vector, assessing the scope of any potential data breach and determing if the attacker gained further access to BookWorld's internal systes
- Protocol of interest: HTTP
- Tool used by attacker: Gobuster
- Investigation focus: Web server reconnaissance, credential leak, php file upload
- File type: PCAP

## Tools Used
- Wireshark
- VirusTotal
- MITRE ATT&CK Navigator

## Investigation Steps
1. Opened a PCAP file gotten from cyberdefenders to analyze a an IDS alert that was flagged
because there was a suspicion activity that was identified in the BookWorld's internal systems.
2. Navigated to statistics, then to conversation to identify top talkers and to determine attacker ( 111.224.250.131 ) and victim ( 73.124.22.98 ) IPs with 88,484 packets.
3. Filtered the traffic with this http and ip.addr == 111.224.250.131.
4. Looked at the traffic and found that the protocols that were being used were HTTP protocols.
5. Wrote a structured finding for each event.

## Findings
1. On Mar 15, 2024 at 11:39 , source IP 111.224.250.131 sent HTTP GET requests to destination IP 73.124.22.98 on port 8080. 
The User-Agent identified the tool as gobuster. The attacker requested GET / HTTP/1.1, GET /css/style.css HTTP/1.1, GET /favicon.ico HTTP/1.1.  
This maps to MITRE ATT&CK technique T1595 - Active Scanning. This indicates that the attacker was performing a reconnaissance attack. 
The attacker wanted to find which part of the webserver was vulnerable and realized that the default page was exposed.

2. On Mar 15, 2024 at 11:39 , source IP 111.224.250.131 sent HTTP GET requests to destination IP 73.124.22.98 on port 8080. 
The User-Agent identified the tool gobuster. The attacker requested GET /admin/login.php HTTP/1.1.
This maps to MITRE ATT&CK technique T1595.003 - Wordlist Scanning. This indicates the attacker used Gobuster to systematically probe the web server for exposed admin panels and directories.

3. On Mar 15, 2024 12:13:04.792571000, source IP 111.224.250.131 sent HTTP GET requests to destination IP 73.124.22.98 on port 8080. 
The User-Agent identified the tool as gobuster. The attacker gained access to the webserve by brute force attack. This maps to MITRE ATT&CK technique T1110 - Brute Force. 
This indicates that the attacker identified the credentials of the web server which happens to be the default credentials of the webs server which were not changed.  

4. Mar 15, 2024 12:13:51.513292000, source IP 111.224.250.131 which is the attacker changes the default password to "changeme" and changed it again to "admin123". This maps to MITRE ATT&CK technique T1098 - Account manipulation. This indicates that the attacker was able to manipulate the account by changing the password twice

5. Mar 15, 2024 12:24:18.822970000, source IP 111.224.250.131 which is the attacker uploads the malicious .php file which is NVri2vhp.php via the admin panel. This maps to MITRE ATT&CK technique T1505.003 - Server Software Component: Web Shell. 
This indicates that the attacker was able to make changes to the webserver by uploading a php file and even establish remote connection to it's C2 servers.
  
6. On Mar 15, 2024 12:24:28.703432000 , source IP 111.224.250.131 which is the attacker accesses the /admin/uploads/NVri2vhp.php to activate the webshell. 
This maps to MITRE ATT&CK technique T1505.003 - Server Software Component: Web Shell. This indicates that the attacker has fully established a hidden backdoor in the web server.

## MITRE ATT&CK Techniques Identified
| Activity | Technique ID | Technique Name |
|----------|-------------|----------------|
|Reconnaissance attack |T1595  | Active scanning |
|brute force attack | T1595.003 | Wordlist scanning |
|brute force to gain access | T1110 | Brute Force |
|change of password | T1098 | Account manipulation |
| webshell | T1505.003 | Webshell |
| Stolen credentials | T1078| Valid Accounts |

## Indicators of Compromise (IOCs)
- Attacker IP: 111.224.250.131
- Victim IP: 73.124.22.98
- Malicious php file: NVri2vhp.php
- Webshell path: /admin/uploads/NVri2vhp.php
- Credentials used: admin:admin, admin:changeme, admin:admin123
- Scanning tool User-Agent: gobuster/3.6


## Conclusion

The attacker first performed a reconnaissance attack using the technique of active scanning. 
The attacker used the gobuster tool for a brute force attack to find exposed panels and directories. 
The attacker gathers the leaked credentials of the webserver which were not changed. 
The attacker was able to gain remote access to the webserver and manipulated the account twice and loaded the malicious php file ( NVri2vhp.php ) to fully establish a backdoor in the webserver. 


## What This Taught Me About SOC Work
* Seeing too many GET requests from a particular IP address using the gobuster tool is a sign that an attacker is trying to gain access into a system.
* Not changing the default credentials of a webserver is a critical vulnerable attack vector for attackers.
* Seeing unexpected php file deployments in the webserver should be flagged as suspicious.
A randomly named application appearing in the deployed application list is an immediate indicator of compromise. 
This gives the attacker persistent remote access and potential escalated privileges on the server.
