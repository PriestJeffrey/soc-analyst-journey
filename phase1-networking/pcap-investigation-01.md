## Incident Report — STRRAT C2 Traffic Analysis

## Scenario
LAN segment data:
LAN segment range:  172.16.1[.]0/24 (172.16.1[.]0 through 172.16.1[.]255)
Domain:  wiresharkworkshop[.]online
Domain controller:  172.16.1[.]4 - WIRESHARK-WS-DC
LAN segment gateway:  172.16.1[.]1
LAN segment broadcast address:  172.16.1[.]255

## Tools Used
- wireshark
- virustotal.com
- abuseipdb.com
- shodan.io
  
## Investigation Steps (step by step what you did)
1. After opening the pcap file, i applied a filter which was ip.src == 172.16.1.0/24 and ip.dst == 172.16.1.0/24
2. It was wrong because, this filter gave me the results of only internal traffic both source and destination
3. I inputted this filter rather ip.src == 172.16.1.0/24 OR ip.dst == 172.16.1.0/24 which showed me the internal and the external traffics.
4. I looked for the infected machine and the hint was to find the ip address address that occurred the most. and it was 172.16.1.66.
5. the way i found it was that, i went to statistics and clicked on conversations which showed me the ip address that occurred the most.
6. I later clicked on the endpoints in the statistics tab which showed me the number of the packet the infected machine has been sending and what external ip addresses it was talking to the most
7. i found out the number of packets it was sending which were 11,531 packets and the external IP it was talking to the most was 141.98.10.79
8. i checked up the ip address on virustotal.com and it was flagged as Malicious by Forcepoint & ThreatSeeker and Suspicious by AlphaSOC.
9. there were no results in abuseipd.com & shodan.io but it didn't mean the traffic was clean. it was the C2 server.
10. i used this filter  ip.addr == 141.98.10.79 to filter the traffic.
11. the attacker used the port 12132 because, the Wireshark software labels it as TCP because it doesn't recognise any standard protocol running on that port. the attacker will decide to use such port because some firewalls will only inspect well known ports. it's just to hide their activities from firewalls so that they can blend with the traffic. the src port was 49754 and the dst port was 12132.
12. i followed the TCP stream to check if the information in the stream were readable texts or they were gibberish and it was readable.
13. the readable texts followed a particular pattern ping|STRRAT|1BE8292C|DESKTOP-SKBR25F|ccollier|Windows 11 Pro|64-bit|Windows Defender
14. so i identifed the hostname, username, OS, the ID of the infection.

## Findings
- Infected IP - 172.16.1.66
- Hostname - DESKTOP-SKBR25F
- Username - ccollier 
- Malware Family - STRRAT 
- C2 Server IP - 141.98.10.79
- C2 Port - src port: 49754 dst: 12132
- Beaconing Interval - every 5 seconds

## Indicators of Compromise (IOCs)
- suspicious IP address ( 141.98.10.79 )
- unusual ports src port: 49754 dst: 12132
- STRRAT malware — Infection ID: 1BE8292C

## Conclusion
The machine DESKTOP-SKBR25F was infected with STRRAT, a Java-based Remote Access Trojan. 
The malware established a C2 channel to 141.98.10.79 on port 12132, beaconing every 5 seconds. 
This gave the attacker persistent remote access to ccollier's machine, including full control over the system at any time

## What This Taught Me About SOC Work
What this lab has taught me about SOC work is that, you need to be vigilant and precise. No guesswork. 
It has also taught me that, you cannot skip the basics in networking.
it has also taught me how knowing tools like wireshark is not an option in soc work.
