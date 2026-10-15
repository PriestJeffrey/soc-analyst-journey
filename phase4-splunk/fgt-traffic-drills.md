## Drills

### Drill 1

### Question
Which destination IP generated the most Fortigate traffic in botsv1? 

### SPL
index=botsv1 earliest=0 sourcetype=fgt_traffic | stats dc(dstip) 

### Evidence
Statistics (1)
Events: 55,279
Top row: 13437 
	
### Finding
The IP address 8.8.8.8 appeared as the loudest destination (5948 of 55,279 events) but it doesn't mean this host is a victim because it appeared to be the loudest. The volume cannot be used to claim it was the victim. This IP address is used as the primary public DNS (Domain Name System) resolver operated by Google.

### MITRE
this search does not map to a technique.


### Drill 2

### Question
Which destination port generated the most Fortigate traffic in botsv1? 

### SPL
index=botsv1 earliest=0 sourcetype=fgt_traffic | stats count by dstport | sort -count

### Evidence
Statistics (105)
Events: 55,279
Top row: 6892 with count 34825

### Finding
The fact that the port is loudest doesn't mean it's the attack. there is no evidence for that. 

### MITRE
this search does not map to a technique.


### Drill 3

### Question
What Break the pile by action mean?

### SPL
index=botsv1 earliest=0 sourcetype=fgt_traffic | stats count by action | sort -count

### Evidence
Statistics (6)

Events: 55,279

| Action | Count|
|-------- | --------- | 
| accept | 30842 |
| start | 8877 |
| deny | 6485 |
|ip-conn | 6188 |
| close | 2885 |
| timeout |	2 |


### Finding
start is not a block. deny is the block 

### MITRE
this search does not map to a technique.
