### Question
Which source IP was denied the most on Fortigate traffic in botsv1? Does that mean the host is the attacker?

### SPL
index=botsv1 earliest=0 sourcetype=fgt_traffic action=deny | stats count by srcip | sort - count

### Evidence
Statistics (661) 
Events: 6,485  
Top row: 192.168.69.104 with count 2970

### Finding
The IP address 192.168.69.104 appeared as the loudest source but it doesn't mean it was an attacker's IP.
The IP is a private IP address which mean the host is inside the same network.

- The loudest IP that was denied was 192.168.69.104 / 2,970
- The loudest talker was 192.168.250.100 with only 199 denies.
If you rank all the traffics, you're likely to suspect and stare at 192.168.250.100 and miss out on this 192.168.69.104. 

### MITRE
this search does not map to a technique.
