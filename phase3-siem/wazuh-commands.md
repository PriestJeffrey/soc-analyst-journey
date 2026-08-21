## Wazuh Manager Commands (Lab Runbook)

### When To Use This
Short list of commands for editing custom rules on Docker Wazuh. Not a full Wazuh install guide.

### 1. Open A Shell In The Manager
- From where: Windows PowerShell (not inside the container)
- Container name: `single-node-wazuh.manager-1`
- Command: docker exec -it single-node-wazuh.manager-1 bash

### 2. Edit And View local_rules.xml
- File path inside container: /var/ossec/etc/rules/local_rules.xml
- Open editor: nano /var/ossec/etc/rules/local_rules.xml
- View file: cat /var/ossec/etc/rules/local_rules.xml
- How to save in nano:
Ctrl + O → Enter (save)
Ctrl + X (exit)

### 3. Reload Wazuh
- Command: /var/ossec/bin/wazuh-control reload
- Success looks like: Completed
- Failure looks like: XMLERR / analysisd CRITICAL — fix XML before testing alerts

### 4. Copy Rules Out (Backup To GitHub)
- What this does: container → Windows
- Command (Desktop example): docker cp single-node-wazuh.manager-1:/var/ossec/etc/rules/local_rules.xml C:\Users\rhema\Desktop\local_rules.xml
- Then upload to: `phase3-siem/local_rules.xml`

### 5. Copy Rules Back In (Restore After Recreate)
- What this does: Windows → container
- Command: docker cp C:\Users\rhema\Desktop\local_rules.xml single-node-wazuh.manager-1:/var/ossec/etc/rules/local_rules.xml
- Then reload (section 3)

### 6. Common Mistakes
- Running `docker` while you are already `root@wazuh`
- Reusing a rule id (e.g. 100002 twice)
- Forgetting reload
- Broken XML (`<field name=<field name=`)

### Related
- [local_rules.xml](local_rules.xml)
- [wazuh-detection-drills.md](wazuh-detection-drills.md)
