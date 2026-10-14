## Investigation - Fortigate traffic

### Question
when do events exist in botsv1?
- I was looking for the IP that appeared the most and to check if it was an attacker's IP or not.

### SPL
index=botsv1 earliest=0 sourcetype=fgt_traffic | stats count by srcip | sort -count

### Evidence
Statistics (671)
Events: 55,279
Top row: 192.168.250.100 with count 36288

### Finding
The IP address 192.168.250.100 appeared as the loudest but it doesn't mean it was an attacker's IP. The volume cannot be used to claim it was the attacker's IP. 

### MITRE
this search does not map to a technique.
