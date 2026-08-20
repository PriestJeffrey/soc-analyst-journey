## Wazuh Custom Detection - cmd.exe spawned whoami.exe (T1033)

### Objective
Write a custom Wazuh rule that alerts on Account Discovery (T1033): cmd.exe spawning whoami.exe.

### How This Differs From Rule 100002
- 100002 = level 0 exclusion (silence a FP)
- 100003 = level 5 detection (create an alert)

### Parent Rule
- Sysmon Event ID 1 grouping rule: **61603** (level 0)
- Child rule: **100003**

### Detection Rule
```xml
<rule id="100003" level="5">
  <if_sid>61603</if_sid>
  <field name="win.eventdata.image">C:\\\\Windows\\\\System32\\\\whoami.exe</field>
  <field name="win.eventdata.parentImage">C:\\\\Windows\\\\System32\\\\cmd.exe</field>
  <description>T1033 Account Discovery - cmd.exe spawned whoami.exe</description>
</rule>
```

### How To Reproduce
1. Open cmd.exe
2. Run whoami
3. Search Wazuh: rule.id:100003

### Validation Evidence
| Field | Value |
|-------|-------|
| Timestamp | 2026-08-20 16:41:13 UTC |
| Rule ID | 100003 |
| Level | 5 |
| Image| C:\Windows\System32\whoami.exe |
| ParentImage | C:\Windows\System32\cmd.exe |
| Decision | True positive (lab-generated recon) |

### What This Taught Me
- Sysmon can record whoami as an Event ID 1 while Wazuh Security events stays empty, because 61603 is level 0. The custom level 5 child rule is what creates the alert.
- After editing local_rules.xml, reload the Wazuh manager or the new rule will not run. 

### Persistence Note
Rule lives in container local_rules.xml. Recreating Docker may wipe it.

### How To Restore After Docker Recreate
From Windows PowerShell (repo `phase3-siem` folder, or use full paths):

docker cp local_rules.xml single-node-wazuh.manager-1:/var/ossec/etc/rules/local_rules.xml
docker exec -it single-node-wazuh.manager-1 /var/ossec/bin/wazuh-control reload
