## Nmap Reconnaissance — SOC Analyst Notes

## What is Nmap?
Nmap is an inductry tool used for reconnaissance. it is used to map a network to check what
devices are on a network and what services those devices are running. 

## Why Attackers Use It
Attackers use nmap to figure out what devices are on a network, they use it to check for
vulnerabilities and what ports are opened on the network.

## Scan Types I Learned

### SYN Scan (-sS)
SYN scans are used to scan the TCP port range of a target or target. SYN scans are also known as
half-opened scans. SYN scans sends back a RST TCP packet after receiving a SYN/ACK from the server
( this prevents the server from repeatedly trying to make the request )

### Full Connect Scan (-sT)
This uses the three way handshake which consists of 3 stages. First, th econnecting terminal sends a TCP
request to the target server with the SYN flag set. the server acknowledges the packet with a TCP response
containing thr SYN flag as well as the ACK flag.
Finally, the terminal completes the handshake by sending a TCP request with the ACK flag set.

## Nmap Commands I Practiced

| Command | What it does |
|--------|--------------|
| `nmap -sS 192.168.1.1` | this uses SYN scan. Sends SYN, gets SYN-ACK, sends RST. It never completes the handshake. it launches a stealthy network scan against the specific device.
| `nmap -sV 192.168.1.1` | Run Nmap against 192.168.1.1 and tries to identify the services the versions running on any open ports.|
| nmap -O 192.168.1.1 | What operating system is this machine likely running? |

## What a SOC Analyst Sees
- One source IP
- HUndreds of SYN packets in a very short time
- Packets hitting sequential or random ports
- No completed three way handshake 
- RST packets after each SYN-ACK response.

## Tools to Investigate a Suspicious IP
| Tool | URL | What it shows |
|------|-----|---------------|
| VirusTotal | virustotal.com | shows if the IP is flagged as malicious |
| AbuseIPDB | abuseipdb.com | shows reported abuse history for that IP |
| Shodan | shodan.io | shows what services the IP address is running publicly |

## Key Takeaways
- Understanding how Nmap works allows a SOC analyst to recognize attacker reconnaissance behavior in network traffic before a full attack is launched.
- Identifying which ports were targeted during a scan allows the SOC analyst to prioritize monitoring on those specific ports and services for follow-up attack activity.
- Documenting every investigation gives a track record of targeted ports, helps you know what to do next time, and avoids guesswork.
