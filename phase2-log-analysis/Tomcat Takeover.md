## Incident Report - Tomcat Takeover

## Scenario
A suspicious activity was identified on a web server within the company's intranet. 
The task was to analyze a PCAP file to understand how the Apache Tomcat web server was compromised
and the malicious activities that led to the compromise of the Apache Tomcat web server.
- Protocol of interest: HTTP
- Tool used by attacker: Gobuster, Tomcat Manager
- Investigation focus: Web server reconnaissance, credential leak, WAR file upload
- File type: PCAP

## Tools Used
- Wireshark
- VirusTotal
- MITRE ATT&CK Navigator

## Investigation Steps
1. Opened a PCAP file gotten from cyberdefenders to analyze a an IDS alert that was flagged
because there was a suspicion activity that was identified on a web server.
2. Navigated to statistics, then to conversation to identify top talkers and to determine attacker and victim IPs
3. Filtered the traffic with this http and ip.addr == 14.0.0.120
4. Looked at the traffic and found that the protocols that were being used were HTTP protocols GET /host-manager/html HTTP/1.1
User-Agent: gobuster/3.6
5. Wrote a structured finding for each event.

## Findings
1. On Sep 10, 2023 at 18:19, source IP 14.0.0.120 sent HTTP GET requests to destination IP 10.0.0.112 on port 8080. The User-Agent identified the tool as gobuster. The attacker requested GET / HTTP/1.1,GET  /tomcat.css, GET /tomcat.png. This maps to MITRE ATT&CK technique T1595 - Active Scanning. This indicates that the attacker was performing a reconnaissance attack. The attacker wanted to find which part of the webserver was vulnerable and realized that the default page was exposed.

2. On Sep 10, 2023 at 18:19, source IP 14.0.0.120 sent HTTP GET requests to destination IP 10.0.0.112 on port 8080. The User-Agent identified the tool as gobuster. The attacker requested /host-manager, /host-manager/html, and /manager. This maps to MITRE ATT&CK technique T1595.003 - Wordlist Scanning. This indicates the attacker used Gobuster to systematically probe the web server for exposed admin panels and directories.

3. On Sep 10, 2023 at 18:19, source IP 14.0.0.120 received a 401 Unauthorized response from the destination IP 10.0.0.112 on port 8080. The User-Agent identified the tool as gobuster. The attacker requested /host-manager/html. This maps to MITRE ATT&CK technique T1589 - Gather Victim Identity Information. This indicates that the attacker identified the credentials of the web server which happens to be the default credentials of the webs server which were not changed. The 401 Unauthorized response leaked the credentials tomcat:s3cret from the server's configuration file tomcat-users.xml. 

4. On Sep 10, 2023 at 18:19:33.485644000, source IP 14.0.0.120 which is the attacker logs into the Tomcat manager using tomcat:s3cret. This maps to MITRE ATT&CK technique T1078 - Valid accounts. This indicates that the attacker was able to log into the Tomcat Manager successfully. The attacker can now make changes or load a malicious .WAR file so that it can make changes in the web server.

5. On Sep 10, 2023 18:22:14.310812000, source IP 14.0.0.120 which is the attacker uploads the malicious WAR file which is JXQOZY.war via the Tomcat Manager. This maps to MITRE ATT&CK technique T1505.003 - Server Software Component: Web Shell. This indicates that the attacker was able to make changes to the webserver and even establish remote connection to it's C2 servers.
  
6. On Sep 10, 2023 18:22:23.099410000, source IP 14.0.0.120 which is the attacker accesses the /JXQOZY/ to activate the webshell. This maps to MITRE ATT&CK technique T1505.003 - Server Software Component: Web Shell. This indicates that the attacker has fully established a hidden backdoor in the web server.

## MITRE ATT&CK Techniques Identified
| Activity | Technique ID | Technique Name |
|----------|-------------|----------------|
|Reconnaissance attack |T1595  | Active scanning |
|brute force attack | T1595.003 | Wordlist scanning |
| gathering victim's identity | T1589 | Gather Victim Identity Information |
| webshell | T1505.003 | Webshell |
| Stolen credentials | T1078| Valid Accounts |

## Indicators of Compromise (IOCs)
- Attacker IP: 14.0.0.120
- Victim IP: 10.0.0.112
- Victim hostname: cyberdefenders-virtual-machine
- Malicious WAR file: JXQOZY.war
- Webshell path: /JXQOZY/
- Credentials used: tomcat:s3cret
- Scanning tool User-Agent: gobuster/3.6
- Tomcat version exposed: Apache Tomcat/7.0.88


## Conclusion

The attacker first performed a reconnaissance attack using the technique of active scanning. The attacker used the gobuster tool for a brute force attack to find exposed panels and directories. The attacker gathers the leaked credentials of the webserver which were not changed. the attacker was able to gain remote access to the webserver and loaded the malicious WAR file ( JXQOZY.war ) to fully establish a backdoor in the webserver. 


## What This Taught Me About SOC Work
* Seeing too many GET requests from a particular IP address using the gobuster tool is a sign that an attacker is trying to gain access into a system.
* Misconfiguring a server can be the single point of attack especially when it contains credentials of the web server
* Seeing unexpected WAR file deployments in the Tomcat Manager should be flagged as suspicious. A randomly named application appearing in the deployed application list is an immediate indicator of compromise. This gives the attacker persistent remote access and potential escalated privileges on the server.
