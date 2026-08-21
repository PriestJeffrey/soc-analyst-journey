## Wazuh Detection Drills - Tune + Custom Rules

### Goal
Practice two exclusion (level 0) and two detection (level 5) without relying on mentor-written XML.

### Rules In This Lab
| ID | Type | Parent SID | Pattern | Result |
|----|------|------------|---------|--------|
| 100002 | Tune | 92910 | Cursor → Explorer 0x40 | (already existed) |
| 100003 | Detect | 61603 | cmd → whoami T1033 | (already existed) |
| 100004 | Tune | 92910 | OMEN → Explorer 0x1410 | no detections afterwards |
| 100008 | Tune | 92910 | OMEN → Explorer 0x1f3fff | no detections afterwards|
| 100005 | Detect | 61603 | cmd → ipconfig T1016 | it detected the alert |
| 100006 | Tune | 92200 | SDXHelper + word-copilot/SolutionPackages | no detections afterwards |
| 100007 | Detect | 61603 | cmd → tasklist T1057 | it detected the alert |

### Drill 1 — OMEN Tune
- The rule required a grantedAccess 0x1410 but the new alert had a different grantedAccess 0x1f3fff. It had the same OMEN process but different permission mark. So the rule fired because there was a mismatch in the grantedAccess.
- 100004 for 0x1410 and 100008 for 0x1f3fff
- searched for rule.id:92910 in wazuh after the reload.

### Drill 2 — ipconfig Detection
- Open cmd.exe
- Run ipconfig
- Search Wazuh: rule.id:100005
- Evidence (2026-08-21T12:08:09.765Z, rule 100005)

### Drill 3 — Office SDXHelper Tune
- the .js has string will change when copilot updates the next time, the exclusion dies and 92200 floods again.
- 100002 is already in cursor's exclusion and if it is reused, it'll overwrite the cursor tune.
- After 100006, 92200 still fired for svchost.exe writing perftrack_summary.json.dup in C:\Windows\Temp. I did not add that to 100006 because it is a different process and file. Same parent rule ≠ same exclusion.

### Drill 4 — tasklist Detection
- Event ID 1 vs Event ID 7
Event ID 1 ( cmd.exe started tasklist.exe ) vs Event ID 7 ( tasklist.exe loaded wmiutils.dll )
- Evidence (2026-08-21T13:05:34.834Z, rule 100007)

### What This Taught Me
- A single tune may not be able to fine tune wazuh to reduce false positives. You may need more than 1 tuning rule.
- After editing local_rules.xml, reload the Wazuh manager or the new rule will not run.

### Rule XML
```xml
<rule id="100004" level="0">
  <if_sid>92910</if_sid>
  <field name="win.eventdata.sourceImage" type="pcre2">OmenCommandCenterBackground\.exe</field>
  <field name="win.eventdata.targetImage">C:\\\\windows\\\\Explorer.EXE</field>
  <field name="win.eventdata.grantedAccess">0x1410</field>
  <description>Suppress OmenCommandCenter Explorer access FP (0x1410)</description>
  <options>no_full_log</options>
</rule>

<rule id="100008" level="0">
  <if_sid>92910</if_sid>
  <field name="win.eventdata.sourceImage" type="pcre2">OmenCommandCenterBackground\.exe</field>
  <field name="win.eventdata.targetImage">C:\\\\windows\\\\Explorer.EXE</field>
  <field name="win.eventdata.grantedAccess">0x1f3fff</field>
  <description>Suppress OmenCommandCenter Explorer access FP (0x1f3fff)</description>
  <options>no_full_log</options>
</rule>

<rule id="100005" level="5">
  <if_sid>61603</if_sid>
  <field name="win.eventdata.image">C:\\\\Windows\\\\System32\\\\ipconfig.exe</field>
  <field name="win.eventdata.parentImage">C:\\\\Windows\\\\System32\\\\cmd.exe</field>
  <description>T1016 System Network Configuration Discovery - cmd.exe spawned ipconfig.exe</description>
</rule>

<rule id="100006" level="0">
  <if_sid>92200</if_sid>
  <field name="win.eventdata.image" type="pcre2">SDXHelper\.exe</field>
<field name="win.eventdata.targetFilename" type="pcre2">SolutionPackages|word-copilot</field>
  <description>Office SDXHelper Copilot JS drop FP</description>
  <options>no_full_log</options>
</rule>

<rule id="100007" level="5">
  <if_sid>61603</if_sid>
  <field name="win.eventdata.image">C:\\\\Windows\\\\System32\\\\tasklist.exe</field>
  <field name="win.eventdata.parentImage">C:\\\\Windows\\\\System32\\\\cmd.exe</field>
  <description>T1057 Process Discovery - cmd.exe spawned tasklist.exe</description>
</rule>
```

### Related Files
- [local_rules.xml](local_rules.xml)
- [wazuh-commands.md](wazuh-commands.md)
