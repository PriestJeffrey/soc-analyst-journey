### Question
What was 192.168.69.104 denied from reaching? Is the loudest dest a victim?

### SPL
index=botsv1 earliest=0 sourcetype=fgt_traffic action=deny srcip=192.168.69.104 | stats count by dstip | sort -count

### Evidence
Statistics (2) 
Events: 2,970
192.168.69.255 / 1,498; 224.0.0.1 / 1,472

### Finding
All 2,970 denies from 192.168.69.104 go to two destinations: 192.168.69.255 (1,498) and 224.0.0.1 (1,472). 
The first event I opened was 224.0.0.1, that was a sample, not the top destination IP. 192.168.69.104 sends to .255 (LAN broadcast) and 224.0.0.1 (multicast). 
Those are not victim hosts. Loudest destination IP is not a victim.

### MITRE
this search does not map to a technique.
