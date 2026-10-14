Question
when do events exist in botsv1?

I was looking for when the logs exist.
SPL
index=botsv1 earliest=0 | stats min(_time) as earliest max(_time) as latest | eval earliest_human=strftime(earliest,"%Y-%m-%d %H:%M:%S") | eval latest_human=strftime(latest,"%Y-%m-%d %H:%M:%S")

Evidence
One Statistics row. earliest_human: 2016-08-10 03:28:51. latest_human: 2016-08-24 18:27:44. (Epoch 1470799731 and 1472063264 before strftime.)

Finding
Events in botsv1 exist from 2016-08-10 03:28:51 to 2016-08-24 18:27:44. That is when logs are in the index. It is not proof the attacker was active for every minute of those 14 days.

MITRE
this search does not map to a technique.
