# Creating a SOC with Honeynet within Microsoft Azure

## Introduction

For this project I utilized Microsoft Azure to create a honeynet and ingest logs from various resources into a Log Analytics workspace. From there, I used Microsoft Sentinel to create attack maps, trigger alerts, and incidents.  I then gathered metrics over a 24-hour period in the insecure environment. From there I applied security controls to harden the environment and repeated the 24-hour period to gather more metrics in the secured environment. I then used the metrics to geographically map the attacker’s locations and summarize the overall improvement after applying security controls. 

<br />

## Metrics Gathered

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents Created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

<br />

## Architecture of the Lab

![Cloud Honeynet / SOC](https://i.imgur.com/jFMrONH.png)
### Technologies, Regulations, and Azure Components Utilized:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 Windows, 1 Linux) 
- Log Analytics Workspace with KQL Queries
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel
- Microsoft Defender for the Cloud
- Windows Remote Desktop
- Command Line Interface
- PowerShell
- NIST SP 800-53 r4
- NIST SP 800-61 r2

<br />

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/x6UdJrr.png)

Initially, all resources were deployed with exposure for the "BEFORE" metrics. The VMs were configured with their NSGs and built-in firewalls set to allow all traffic, and all other resources were also deployed with public endpoints that were visible to the internet. Consequently, Private Endpoints were not utilized.

<br />

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/l91mgkr.png)

To improve the "AFTER" metrics, NSGs were made more secure by prohibiting ALL traffic except for my admin workstation, while all other resources were safegaurded by their own built-in firewalls in addition to Private Endpoints.

<br />

## Attack Maps BEFORE Hardening / Security Controls
![NSG Allowed Inbound Malicious Flows](https://i.imgur.com/hP91wFgl.jpg)

<br />

![Linux Syslog Auth Failures](https://i.imgur.com/Lwbtvrfl.jpg)

<br />

![Windows RDP/SMB Auth Failures](https://i.imgur.com/F7XxAhql.jpg)

<br />

![MSSQL Auth Failures](https://i.imgur.com/fmAeMdfl.jpg)

<br />

## Metrics Before Hardening / Security Controls

Measured metrics in the insecure environment for 24 hours:
Start Time 2023-04-09 11:30 AM EST
Stop Time 2023-04-10 11:30 AM EST

| Metric                                          | Count
| ----------------------------------------------- | -----
| SecurityEvents (Windows VMs)                    | 34,063
| Syslog (Linux VM)                               | 7783
| SecurityAlert (Microsoft Defender for Cloud)    | 12
| SecurityIncident (Sentinel Incidents)           | 295
| NSG Inbound Malicious Flows Allowed             | 624

<br />

## Hardening Steps

The initial 24-hour study revealed that the lab was vulnerable to multiple threats due to its visibility on the public internet. To address these findings, I activated NIST SP 800-53 r4 within the compliance section of Microsoft Defender and focused on fulfilling the compliance standards associated with SC.7.*. Additional assessments for SC-7 - Boundary Protection.

![Hardening](https://i.imgur.com/Ac5PNYQl.jpg)
       
<br />

## Attack Maps AFTER Hardening / Security Controls

```There were no results to display for the 24 hour repeat map queries following the hardening of the assets. ```

<br />

## Metrics After Hardening / Security Controls

Measured metrics in the secure environment for another 24 hours after applying security controls:
Start Time 2023-04-10 11:30 AM EST
Stop Time	2023-04-11 11:30 AM EST

| Metric                                          | Count
| ----------------------------------------------- | -----
| SecurityEvents (Windows VMs)                    | 11,679
| Syslog (Linux VM)                               | 33
| SecurityAlert (Microsoft Defender for Cloud)    | 0
| SecurityIncident (Sentinel Incidents)           | 10
| NSG Inbound Malicious Flows Allowed             | 30

<br />

## Overall Improvement

| Metric                                          | Count
| ----------------------------------------------- | -----
| SecurityEvents (Windows VMs)                    | -65.71%
| Syslog (Linux VM)                               | -99.58%
| SecurityAlert (Microsoft Defender for Cloud)    | -100%
| SecurityIncident (Sentinel Incidents)           | -96.61%
| NSG Inbound Malicious Flows Allowed             | -95.19%

![48 Hour Improvement](https://i.imgur.com/eUzTyCDl.png)

<br />

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastially reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.

<br />

## Simulated Attacks

While the environment was configured for vulnerabilities, I took the opportunity simulate specific attacks via PowerShell scripts or by manually triggering. The results were observed in Log Analytics Workspace and Sentinel Incident Creation.  

- Linux Brute Force Attempt 
- AAD Brute Force Success 
- Windows Brute Force Success
- Malware Detection (EICAR Test File) 
- Privilege Escalation  

![Attacker](https://i.imgur.com/CpoVQw7l.png)

<br />

## Utilizing NIST 800.61r2 Computer Incident Handling Guide

For each simulated attack I then practiced incident responses following NIST SP 800-61 r2.

![NIST 800.61](https://i.imgur.com/6PTG7c0l.png)

One such event was the detection of malware on a workstation. Each organization will have policies related to incident response and should be followed. 

#### Preparation

- The Azure lab was setup to ingest all of the logs into Log Analytics Workspace, Sentinel and Defender configured, and alert rules put in place.

#### Detection & Analysis

- Malware has been detected on a workstation with the potential to compromise the confidentiality, integrity, or availability of the system and data.
- Assigned alert to an owner, set the severity to "High", and the status to "Active"
- Identified the primary user account of the system and all systems affected.
- A full scan of the system was conducted using an up-to-date antivirus software to identify the malware.
- Verified the authenticity of the alert as a "True Positive".
- Sent notifications to appropriate personnel as required by the organization's communication policies.

#### Containment, Eradication & Recovery

- The infected system and any additional systems infected by the malware were quarantined.
- If the malware was unable to be removed or the system sustained damage, the system would have been shutdown and disconnected from the network.
- Depending on organizational policies the affected systems could be restored known clean state, such as a system image or a clean installation of the operating system and applications. Or the an up-to-date anti-virus solution could be used to clean the systems. 

#### Post-Incident Activity

- In this simulated case an employee had downloaded a game that contained the malware. 
- 

