## Wazuh Detection Tuning — Cursor.exe / Rule 92910

### Objective
Reduce false positive noise from legitimate Cursor → Explorer process access (T1055 / rule 92910) without blinding real Explorer access alerts.

### What I Changed
- Host: Docker container `single-node-wazuh.manager-1`
- File: `/var/ossec/etc/rules/local_rules.xml`
- Action: added child rule `100002` (level 0) under `if_sid` 92910

### Exclusion Rule
```xml
<rule id="100002" level="0">
  <if_sid>92910</if_sid>
  <field name="win.eventdata.sourceImage">C:\\\\Users\\\\rhema\\\\AppData\\\\Local\\\\Programs\\\\cursor\\\\Cursor.exe</field>
  <field name="win.eventdata.targetImage">C:\\\\windows\\\\Explorer.EXE</field>
  <field name="win.eventdata.grantedAccess">0x40</field>
  <description>Suppress Cursor.exe Explorer access FP (0x40)</description>
  <options>no_full_log</options>
</rule>
```

### Conditions (narrow)
- sourceImage = official Cursor path
- targetImage = Explorer.EXE
- grantedAccess = 0x40

### Before / After
- Before: rule 92910 fired repeatedly for Cursor (firedtimes had reached 74 earlier)
- After: reproduced File→Open in Cursor on 14/08/2026 02:55pm ; no new matching 92910 Cursor alert in last 15 minutes

### Why Not Delete 92910
Deleting 92910 would hide all Explorer process-access injection-style alerts. Level 0 child only suppresses this lab FP pattern.

### Risk if Mistuned
Too-broad Cursor/Explorer exclusions could hide real process injection.

### Persistence Note
Rule was edited inside the Docker container filesystem. Recreating the container may wipe it unless the rules file is mounted/persisted.

### Related Investigation
- [Cursor → Explorer triage](Alert%20Investigation%20-%20cursor-explorer.md)
