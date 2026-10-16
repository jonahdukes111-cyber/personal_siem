# PowerShell Encoded Command Detection

**Objective:**
 - Develop a custom Elastic Security detection rule that identifies when a PowerShell process is executed with encoded command arguments on a monitored Windows 11 endpoint.

**Detection Logic:**
   - Rule Name: `Suspicious Powershell Encoded Command Execution`
  
   - Rule Type: Custom Query; KQL
  
   - Rule Severity: Medium

   - MITRE ATT&CK: T1059.001 -- Powershell Technique
     
**Index Pattern:**

`logs-endpoint.events.process-*`

**KQL Query:**

`event.category: "process" and process.name: "powershell.exe"  and process.command_line: *EncodedCommand*`

This rule is scheduled to run every minute, and it will identify when a PowerShell process is executed with encoded command arguments. This technique is used to obfuscate the contents of a script. It needs to be investigated, though, as legitimate tools may use encoded commands as well.

To test this rule, I used a simple powershell script using two variables. One being the command ($command), and one being the command encoded into a base64 string.
```
$command = 'Write-Output "Encoded Command Test"'

$encoded = [Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($command))

powershell.exe -NoProfile -EncodedCommand $encoded
```
**Results:** 

 - Elastic Defend successfully logged the Powershell process execution.
 - This was then stored in Elasticsearch, and verified on the Kibana dashboard.
 - Once the custom rule ran, it successfully generated an alert under: `Security -> Detections -> Alerts`.

When I tried implementing this originally, the index did not match any Elasticsearch indices. I had to modify it so that it would successfully generate an alert when it was supposed to.
 - as a result, though, some shortened arguments may slip past the logic.

**Screenshots:**
 - The generated security alert:
   
<img width="2557" height="1298" alt="Screenshot 2026-10-15 143305" src="https://github.com/user-attachments/assets/5482a67f-1c3e-41b5-8bdb-8a589f30ff67" />
 
 - Rule configuration:
   
<img width="1913" height="940" alt="Screenshot 2026-10-15 134345" src="https://github.com/user-attachments/assets/4b218f60-467d-4045-bbc7-820818b36b51" />

 - Matching Event in Discover:
   
<img width="1915" height="941" alt="Screenshot 2026-10-15 134705" src="https://github.com/user-attachments/assets/d879fb17-0fe7-4395-a454-b25780095aa0" />



