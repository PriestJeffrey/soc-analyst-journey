## Investigation - Finding the destination address.

### Question
Which destination IP generated the most Fortigate traffic in botsv1? 

### SPL
index=botsv1 earliest=0 sourcetype=fgt_traffic | stats count by dstip | sort -count

### Evidence
Statistics (10,000)
Events: 55,279
Top row: 8.8.8.8 with count 5948
	
### Finding
The IP address 8.8.8.8 appeared as the loudest destination (5948 of 55,279 events) but it doesn't mean this host is a victim because it appeared to be the loudest. The volume cannot be used to claim it was the victim. This IP address is used as the primary public DNS (Domain Name System) resolver operated by Google.

### MITRE
this search does not map to a technique.
