## Investigation - Fortigate traffic

### Question
Which source IP generated the most Fortigate traffic in botsv1? Does that volume mean the host is the attacker?

### SPL
index=botsv1 earliest=0 sourcetype=fgt_traffic | stats count by srcip | sort -count

### Evidence
Statistics (671)
Events: 55,279
Top row: 192.168.250.100 with count 36288

### Finding
The IP address 192.168.250.100 appeared as the loudest source (36,288 of 55,279 events) but it doesn't mean it was an attacker's IP. The volume cannot be used to claim it was the attacker's IP. This IP is private/internal so looking it up in VirusTotal does not apply.

### MITRE
this search does not map to a technique.
