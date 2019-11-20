| Title                | XSL Script Processing                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Extensible Stylesheet Language (XSL) files are commonly used to describe the processing and rendering of data within XML files, rule detects when adversaries abuse this functionality to execute arbitrary files while potentially bypassing application whitelisting defenses                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0002: Execution](https://attack.mitre.org/tactics/TA0002)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1220: XSL Script Processing](https://attack.mitre.org/techniques/T1220)</li></ul>  |
| Data Needed          |  There is no documented Data Needed for this Detection Rule yet  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1220: XSL Script Processing](../Triggers/T1220.md)</li></ul>  |
| Severity Level       | medium |
| False Positives      | <ul><li>WMIC.exe FP depend on scripts and administrative methods used in the monitored environment</li><li>msxsl.exe is not installed by default so unlikely.</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/T1220/T1220.yaml](https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/T1220/T1220.yaml)</li></ul>  |
| Author               | Timur Zinniatullin, oscd.community |


## Detection Rules

### Sigma rule

```
title: XSL Script Processing
id: 05c36dd6-79d6-4a9a-97da-3db20298ab2d
status: experimental
description: Extensible Stylesheet Language (XSL) files are commonly used to describe the processing and rendering of data within XML files, rule detects when adversaries
    abuse this functionality to execute arbitrary files while potentially bypassing application whitelisting defenses
author: Timur Zinniatullin, oscd.community
date: 2019/10/21
modified: 2019/11/04
references:
    - https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/T1220/T1220.yaml
logsource:
    category: process_creation
    product: windows
detection:
    selection:
      - Image|endswith: '\wmic.exe'
        CommandLine|contains: '/format' # wmic process list /FORMAT /?
      - Image|endswith: '\msxsl.exe'
    condition: selection
falsepositives:
    - WMIC.exe FP depend on scripts and administrative methods used in the monitored environment
    - msxsl.exe is not installed by default so unlikely.
level: medium
tags:
    - attack.execution
    - attack.t1220

```





### splunk
    
```
((Image="*\\\\wmic.exe" CommandLine="*/format*") OR Image="*\\\\msxsl.exe")
```






### Saved Search for Splunk

```
Generated with Sigma2SplunkAlert
[XSL Script Processing]
action.email = 1
action.email.subject.alert = Splunk Alert: $name$
action.email.to = test@test.de
action.email.message.alert = Splunk Alert $name$ triggered \
List of interesting fields:   \
title: XSL Script Processing status: experimental \
description: Extensible Stylesheet Language (XSL) files are commonly used to describe the processing and rendering of data within XML files, rule detects when adversaries abuse this functionality to execute arbitrary files while potentially bypassing application whitelisting defenses \
references: ['https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/T1220/T1220.yaml'] \
tags: ['attack.execution', 'attack.t1220'] \
author: Timur Zinniatullin, oscd.community \
date:  \
falsepositives: ['WMIC.exe FP depend on scripts and administrative methods used in the monitored environment', 'msxsl.exe is not installed by default so unlikely.'] \
level: medium
action.email.useNSSubject = 1
alert.severity = 1
alert.suppress = 0
alert.track = 1
alert.expires = 24h
counttype = number of events
cron_schedule = */10 * * * *
allow_skew = 50%
schedule_window = auto
description = Extensible Stylesheet Language (XSL) files are commonly used to describe the processing and rendering of data within XML files, rule detects when adversaries abuse this functionality to execute arbitrary files while potentially bypassing application whitelisting defenses
dispatch.earliest_time = -10m
dispatch.latest_time = now
enableSched = 1
quantity = 0
relation = greater than
request.ui_dispatch_app = sigma_hunting_app
request.ui_dispatch_view = search
search = ((Image="*\\wmic.exe" CommandLine="*/format*") OR Image="*\\msxsl.exe") | stats values(*) AS * by _time | search NOT [| inputlookup XSL_Script_Processing_whitelist.csv] | collect index=threat-hunting marker="sigma_tag=attack.execution,sigma_tag=attack.t1220,level=medium"
```