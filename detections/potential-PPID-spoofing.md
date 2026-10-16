# Objective  
 - Determine whether or not a process has been spawned/started with a PPID not matching the actual process's PID that started it.
 - 
**Detection Logic**
 - Rule Name: `Potential Parent PID Spoofing`
 - Rule Type: ES|QL
 - Rule Severity: Medium
 - MITRE ATT&CK: T1134
   
**ES|QL Query:** 
```
FROM lab-ppid-test 
| WHERE KQL("event.category:'process' and event.type: 'start'") -- KQL query used to narrow down the data being queried.
| WHERE process.parent.pid IS NOT NULL and process.parent.Ext.real.pid IS NOT NULL
| WHERE process.parent.pid != process.parent.Ext.real.pid
```
This rule is scheduled to run every minute, and it will check to see if a process is started, and if it's PPID (parent process identifier) does not match the actual parent processes PID (parent identifier). If this is the case, it will generate an alert.

**Testing Method:**

When I tested this rule, I used the Elastic console to log a simulated event using JSON that would satisfy the query created:

```
POST lab-ppid-test/_doc
{
  "@timestamp": "2026-10-16T11:00:00UTC-5",
  "event": {
    "category": "process",
    "type": "start"
  },
  "host": {
    "name": "windows-endpoint-test"
  },
  "process": {
    "name": "notepad.exe",
    "pid": 5000,
    "parent": {
      "name": "explorer.exe",
      "pid": 4000,
      "Ext": {
        "real": {
          "pid": 3000
        }
      }
    }
  },
  "tags": ["ppid-test"]
}
```

This was the result in the Kibana's discovery tab, with the ES|QL query from above, with the simulated data source `lab-ppid-test` instead of the actual Windows endpoint VM:

<img width="1917" height="941" alt="Screenshot 2026-10-16 113929" src="https://github.com/user-attachments/assets/aee0b981-eb9a-4941-ba9f-d4dc9be6bac4" /> 


# **Screenshots:**
  - Rule Definition:
    <img width="1916" height="938" alt="Screenshot 2026-10-16 114058" src="https://github.com/user-attachments/assets/ec2eecdf-fd5a-4216-a605-155ee35d1a5b" />

  - Simulated event in the Kibana Devtools console:
    <img width="1918" height="940" alt="Screenshot 2026-10-16 113946" src="https://github.com/user-attachments/assets/5a4dc839-331c-495e-a5b6-c733c17ecd76" />






