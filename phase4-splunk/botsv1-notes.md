# BOTS v1 - ingest

## Objective
Search a multi-source incident in Splunk (index=botsv1) and split 955,807 events by sourcetype before investigating.

## Lab
- Tool: Splunk Enterprise (local)
- Dataset: BOTS v1 pre-indexed app (not Buttercup, not the JSON dump)
- Index: botsv1 (not main)

## Proof
| Item | Value |
|------|--------|
| SPL | index=botsv1 earliest=0 | stats count by sourcetype | sort -count |
| Time | All time / earliest=0 |
| Events | 955,807 |
| Statistics rows (sourcetypes) | 22 sourcetypes |
| Top sourcetype | xmlwineventlog:microsoft-windows-sysmon/operational |
| Top count | 270597|

Why `index=botsv1` was required (vs default `main`):
- Without index=botsv1, Splunk searches main (Buttercup). This incident is not there.
You either get 0 BOTS events or you mix tutorial logs with the incident if you search too broadly.

Why `earliest=0` was required:
- earliest=0 (or All time) is required because BOTS timestamps are 2016. Last 24 hours returns 0 events.

## What this index contains
xmlwineventlog:microsoft-windows-sysmon/operational	(270597), stream:smb, suricata, wineventlog:security. The index contains logs from endpoint + network + IDS incidents. 
