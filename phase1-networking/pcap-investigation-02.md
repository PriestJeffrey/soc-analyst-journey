## Incident Report — Likely Koi Stealer C2 Traffic Analysis

## Scenario
LAN segment details:
LAN segment range:  172.17.0[.]0/24 (172.17.0[.]0 through 172.17.0[.]255)
Domain:  bepositive[.]com
Active Directory (AD) domain controller:  172.17.0[.]17 - WIN-CTL9XBQ9Y19
AD environment name:  BEPOSITIVE
LAN segment gateway:  172.17.0[.]1
LAN segment broadcast address:  172.17.0[.]255

## Tools Used
- wireshark
- virustotal.com
- abuseipdb.com
- shodan.io
  
## Investigation Steps (step by step what you did)
1. After opening the pcap file, i applied a filter which was ip.src == 172.17.0.0/24 or ip.dst == 172.17.0.0/24
2. I inputted this filter ip.src == 172.17.0.0/24 or ip.dst == 172.17.0.0/24 which showed me the internal and the external traffics.
3. I looked for the infected machine and the hint was to find the ip address address that occurred the most and it was 172.17.0.99.
4. the way i found it was that, i went to statistics and clicked on conversations which showed me the ip address that occurred the most.
5. I later clicked on the endpoints in the statistics tab which showed me the number of the packet the infected machine has been sending and what external ip addresses it was talking to the most
6. i found out the number of packets it was sending which were 4,793 packets and the external IP it was talking to the most was 79.124.78.197.
7. i checked up the ip address on virustotal.com and it was flagged as Malicious by Gridinsoft.
8. there were no results in abuseipd.com & shodan.io but it didn't mean the traffic was clean. it was the C2 server.
9. i used this filter  ip.addr == 79.124.78.197 to filter the traffic.
10. I looked at the protocol and it was a TCP & HTTP and the ports were quite unusal. TCP src port 49813 & dst 80 http src 80 dst 49813
11. i followed the HTTP stream to check if the information in the stream were readable texts or they were gibberish and it was readable.
12. the readable texts followed a particular pattern

POST /foots.php HTTP/1.1
Content-Type: application/octet-stream
Content-Encoding: binary
User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; WOW64; Trident/7.0; .NET4.0C; .NET4.0E; .NET CLR 2.0.50727; .NET CLR 3.0.30729; .NET CLR 3.5.30729)
Host: 79.124.78.197
Content-Length: 94
Connection: Keep-Alive
Cache-Control: no-cache

HTTP/1.1 200 OK
Server: nginx
Date: Wed, 04 Sep 2024 17:35:09 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive

13. The HTTP method that was being used was the POST method. An HTTP POST request is a method used to send data to a web server to create a new resource,
update existing information, or trigger a specific action. The infected machine was repeatedly sending POST requests to an external IP. And that is pure data exfiltration.
14. User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; WOW64) MSIE 7.0 is Internet Explorer 7.
That browser was release in 2006 and has been dead for years. No legitimate software in 2024 uses that User-Agent. it's purely a disguise technique to blend into web traffic
and avoid triggering modern security filters that look for known malware signatures.
15. POST /foots.php HTTP/1.1.
A .php file on an external server receiving binary data repeatedly. That's not a legitimate website.
That's a C2 receiver script. The attacker set up a PHP script to collect data being sent from infected machines.
so in essence the malware is exfiltrating data by hiding it's identity and has created a .php file on an external server to receive binary data repeatedly

13. so i identifed the hostname, username, OS, the ID of the infection.

## Findings
- Infected IP - 172.17.0.99
- Malware Family: Unconfirmed. Likely Koi Stealer based on traffic patterns and C2 behavior.
- C2 Server IP - 79.124.78.197
- C2 Port - src port: 80 dst: 49813
- Protocol used: HTTP

## Indicators of Compromise (IOCs)
- POST /foots.php HTTP/1.1
- User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; WOW64)

## Conclusion
The machine with the IP 172.17.0.99 was infected with likely Koi Stealer based on the traffic patterns.
The malware established a C2 channel to 79.124.78.197. 
This gave the attacker the access to exfiltrate data to a .php file on an external server to receive binary data repeatedly. 

## What This Taught Me About SOC Work
- A User-Agent string claiming to be Internet Explorer 7 in 2024 traffic is an immediate indicator of compromise.
- Attackers use HTTP for C2 communication because it allows them to blend with legitimate traffic and avoid detection.
- Repeated POST requests from an internal machine to an external IP are a strong signal that data exfiltration is occurring.
