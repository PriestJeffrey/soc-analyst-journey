### Drill 1
### What was the Loudest destination port?

### SPL
index=botsv1 earliest=0 sourcetype=fgt_traffic action=deny srcip=192.168.69.104 | stats count by dstport| sort -count

### Evidence
Pile: (event count — 2,970 for drills 1)
Top row: destination port: 8612	and count: 2944

### Finding
the destnation port 8612 was the loudest and it doesn't mean that's the port the attacker used. 2,944 of 2,970 denies from this host are port 8612.


### Drill 2
### How do the 2,970 denies from 192.168.69.104 split by protocol?

### SPL
index=botsv1 earliest=0 sourcetype=fgt_traffic action=deny srcip=192.168.69.104 | stats count by proto | sort -count

### Evidence
Pile: (event count — 2,970 for drills 2)
proto=17 / 2,970

### Finding
all 2,970 are UDP; that does not mean the attacker used UDP.
