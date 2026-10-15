# Lab Environment

Here, I will describe the configuration, installation, and infrastructure involved in this self-hosted SIEM (Security Incident and Event Management)/SOC lab.

- I decided to use Elastic Solutions for this lab. I utilized Elasticsearch, Kibana (Dashboard/monitoring), Elastic Agents, and Elastic Defend (endpoint monitoring) to analyze and detect suspicious activity on a Windows 11 system.

- I hosted this lab utilizing VMWare Workstation Pro. I used two virtual machines, one used to host an Ubuntu 24.04 Server, and another one to host a Windows 11 machine.

- The goal of this lab was to make a centralized SIEM, monitor Windows process execution & security events, implement custom detection rules & alerts, and provide a foundation for Python incident response implementation.
  
## Physical Host Specifications
This hardware is what hosted the virtual enviroment.

|**Component**|**Specification**|
| ----------|---------------|
|Operating System|Windows 11|
|CPU|Ryzen 7 9800X3D|
|Memory|64GB DDR5|
|Storage|4TB NVMe M.2 SSD|
|Hypervisor|VMWare Workstation Pro|
|VMWare Version|26.0.0|
# Ubuntu SIEM Server Configuration
|**Component**|**Specification**|
| ----------|---------------|
|Operating System|Ubuntu Server 24.04|
|vCPUs|4|
|Allocated Memory|8GB|
|Storage|60GB Allocated|
|Network Adapter|VMWare NAT|
|IP Address|192.168.63.130 (NAT)|

**Installed Software:**
  - Elasticsearch 9.5.5
  - Kibana 9.5.5
  - Fleet Server
  - OpenSSH Server

This VM contained the core functionality of the SIEM. Elasticsearch is used for parsing through events that are collected. Kibana is a dashboard that provides visual dashboard for management, investigation, & monitoring. Fleet Server manages Elastic Agents, which are used for endpoint monitoring/threat hunting.
# Windows 11 Endpoint Configuration
|**Component**|**Specification**|
| ----------|---------------|
|Operating System|Windows 11|
|vCPUs|4|
|Allocated Memory|8GB|
|Storage|64GB Allocated|
|Network Adapter|VMWare NAT|
|IP Address|192.168.63.131 (NAT)|

**Installed Software:**
  - Elastic Agent
  - Elastic Defend

This VM simulated an endpoint to be monitored. It generates data that is collected and logged by Elastic Defend, parsed through with Elasticsearch, and visualized with Kibana. Elastic Defend is an agent integration that provides telemetry that gives analysts the ability to investigate, analyze, and prevent threats on hosts. 
# Network Configuration
|**Service**|**Port**|**Purpose**|
|-------------|--------|-----------|
|SSH|22|Remote Administration/Access|
|Elasticsearch|9220|Data parsing and ingestion|
|Kibana|5601|SIEM management|
|Fleet Server|8220|Elastic Agent management|
# Elasticsearch Installation
Elasticsearch was installed on the Ubuntu Server VM using the Linux .tar.gz archive distribution.

The application was started using:
```
cd ~/elasticsearch-9.5.5
./bin/elasticsearch
```
After starting, the node-cluster was reported healthy(green).
# Kibana Installation
Kibana 9.5.5 was also installed using the Linux .tar.gz archive distribution.
The application was starting using:
```
cd ~/kibana-9.5.5
./bin/kibana
```
- Kibana Configuration
  - Kibana was configured to accept connections from all IPv4 interfaces assigned to the Ubuntu VM. I did this so I could connect to the dashboard on my physical Windows host via NAT. This is done through the kibana.yaml file, where this line was added: `server.host: "0.0.0.0"`.
 
  - Encryption keys required for various saved objects were also added to this configuration for Kibana security.

Once Kibana was configured, I accessed the dashboard through the Ubuntu VM's NAT IPv4 address on my physical Windows host via port 5601.
# Fleet Server Configuration
Fleet servers are used to manage Elastic Agents.
The URL for the fleet server used in this lab: `https://192.168.63.130:8220`
  - Fleet server policy name: Fleet-Server-Policy
    
  - It was configured through Kibana's fleet management interface.

Once the server was established, I viewed its health on the Kibana dashboard via the path: `Kibana -> Management -> Fleet -> Agents`. It was confirmed to be healthy, so I continued on with the endpoint configuration.
# Windows VM Endpoint Configuration
Elastic Agent was installed on this virtual machine, and enrolled into my fleet on Kibana. This was the command used: 

    
    & "C:\Program Files\Elastic\Agent\elastic-agent.exe" enroll `
    --url=https://192.168.63.130:8220 `
    --enrollment-token=<MY_TOKEN> `
    --insecure
    
 - I used a self-signed TLS certificate. Because the lab is isolated, I used the --insecure argument when enrolling the agent as my Windows VM endpoint did not trust the certificate given from the Fleet Server on the Ubuntu VM.

**Elastic Defend**
Elastic defend was added as an integration on the policy for the Windows endpoint agent. This integration logs activity relevant to system security according to the OS.

This logging includes:
  - Processes
  - Network activity
  - Files
  - Registry
  - DNS
  - DLL and driver loading
  - API
  - Security

To verify that the Windows VM was successfully added as an endpoint, I used this path via the Kibana dashboard: `Security -> Manage -> Endpoints`.

# Troubleshooting during setup
In my original plan for this lab, I was going to use Oracle as my VM software, but I repeatedly ran into virtualization errors so I decided to go with VMWare Workstation Pro.
 - Wazuh was also going to be my security platform of choice. I ran into issues, though, during it's installation on the Oracle VM. After running the executable installation file, I kept running into issues during setup - specifically with Wazuh Server.

Another issue I faced was initially creating the Kibana fleet server. I had to generate, and add encryption keys to the Kibana.yaml configuration file to get past this as instructed in the documentation.
 - This is to keep potentially sensitive settings secure.

Enrolling the Windows 11 VM's agent into the fleet server also required me to troubleshoot the `x509: certificate signed by unknown authority` Elastic Stack error. I used the --insecure argument in powershell to disable the TLS certificate verification. I only did this due to it being an isolated lab, and wouldn't do this in an actual production environment as it can be exploited very easily.

# Project Status

Lab VM's are all operational, Elastic stack is functional, and telemetry is being collected and logged.

The first detection rule based on MITRE ATT&CK T1059.001 (Encoded powershell command execution) has been created and is functional. The second detection rule is based on the MITRE ATT&CK T1110 (Brute forcing).

I plan on adding more detection rules based on MITRE ATT&CK techniques, and adding automated alert investigation via Kibana.

I also want to create an automated incident response and detection program using Python.





 
     


