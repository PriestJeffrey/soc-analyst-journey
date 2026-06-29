## Why Ports Matter in Investigations
Not all ports are equal. There are three ranges:
Range Ports What they are;
## Well-known 
0 - 1023 Assigned to standard services. 
Port 80 = HTTP, 443 = HTTPS, 22 = SSH 
## Registered 
1024 - 49151 Assigned to specific applications but less strictly enforced 
## Dynamic/Ephemeral 
49152 - 65535 Temporary ports your machine assigns to itself when it initiates a connection

## The SOC lesson: 
When Wireshark labels traffic as just "TCP" with no protocol name, it means it doesn't recognize any standard protocol on that port. 
Attackers use obscure ports deliberately because some firewalls only inspect well-known ports, and unknown ports blend into noise.

## Real example from my  STRRAT investigation:
Source port 49754 = ephemeral port (normal, your machine initiated the connection)
Destination port 12132 = no recognized protocol, not a standard service
Combined with beaconing every 5 seconds to a flagged IP = confirmed C2 traffic

## How to articulate a suspicious port in a report:
Don't write "the port was unusual." Write: "Traffic on port 12132 had no recognizable protocol signature. 
Wireshark labeled it as raw TCP. Combined with regular 5-second beaconing intervals to a VirusTotal-flagged external IP, 
his indicates deliberate use of a non-standard port to avoid firewall inspection."
