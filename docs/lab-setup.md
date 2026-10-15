# Lab Environment

Here, I will describe the configuration, installation, and infrastructure involved in this self-hosted SIEM (Security Incident and Event Management)/SOC lab.

- I decided to use Elastic Solutions for this lab. I utilized Elasticsearch, Kibana (Dashboard/monitoring), Fleet Server, and Elastic Defend (endpoint monitoring) to analyze and detect suspicious activity on a Windows 11 system.

- I hosted this lab utilizing VMWare Workstation Pro. I used two virtual machines, one used to host an Ubuntu 24.04 Server, and another one to host a Windows 11 machine.

- The goal of this lab was to make a centralized SIEM, monitor Windows process execution & security events, implement custom detection rules & alerts, provide a foundation for Python incident response implementation.
  
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
|IP Address|192.168.63.130|

**Installed Software:**
  - Elasticsearch 9.5.5
  - Kibana 9.5.5
  - Fleet Server
  - OpenSSH Server

This VM contained the core functionality of the SIEM. Elasticsearch is used for indexing and storing events that are collected. Kibana is a dashboard that provides management, investigation, & monitoring. Fleet Server manages Elastic Agents, which are used for endpoint monitoring/threat hunting.
# Windows 11 Endpoint Configuration
|**Component**|**Specification**|
| ----------|---------------|
|Operating System|Windows 11|
|vCPUs|4|
|Allocated Memory|8GB|
|Storage|64GB Allocated|
|Network Adapter|VMWare NAT|
|IP Address|192.168.63.131|
