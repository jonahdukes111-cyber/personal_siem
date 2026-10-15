# Objective
Create a threshold-based Elastic Security detection rule that identifies when 5 or more failed logons happen repeatedly on a Windows 11 endpoint.

**Detection Logic:**

 - Rule name: Repeated Failed Logons
 - Rule type: Threshold
 - Windows Event ID: 4625
 - Threshold: 5 events
 - Grouped by: host.name
 - Severity: Medium
 - MITRE ATT&CK T1110 -- Brute Force

**Index Pattern:**
 - `logs-*`

**KQL Query:**
 - `event.code: "4625"`
This query will be used against the index pattern to find repeated logon failures grouped by the same host name. If it finds 5 or more, it will generate an alert.

**Testing Method:**

In order to test this rule, I began to SSH into the Windows 11 using its NAT IPv4 address, but purposely entered the incorrect user password 5 times. This generated events with the 4625 ID.

 - **Results:**
    - Windows recorded the failed logon events
    - Elasticsearch received the events as they were collected and logged by Endpoint Defend
    - The 5 events met the threshold rule defined by 5 failed logon events.
    - The rule generated an alert under: `Security -> Detections -> Alerts`.

**Limitations:**

  - With a low threshold, legitmiate failed logons may trigger an alert.
  - Grouping by host.name doesn't tell us what account could be targeted, or if the logons are happening from multiple source IP's.
  - Production use of a rule like this would need more nuance and tuning before implementation.

**Screenshots:**

 - Rule definition:
   
<img width="2550" height="1287" alt="Screenshot 2026-10-15 142912" src="https://github.com/user-attachments/assets/f8ca37c2-d749-47b7-a101-0115c03c7666" />

- Discover events:

<img width="2556" height="1300" alt="Screenshot 2026-10-15 143018" src="https://github.com/user-attachments/assets/cef86dc6-c9a0-4890-ac08-c955ce7dc97c" /> 

- Generated alert:

<img width="2555" height="1294" alt="Screenshot 2026-10-15 143122" src="https://github.com/user-attachments/assets/04f77496-5d14-497a-9886-6119e5c6f6fe" />



