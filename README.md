# Building a SOC + Honeynet in Azure (Live Traffic)
![Cloud Honeynet / SOC](https://i.imgur.com/jFMrONH.png)

<br />

## Introduction

In this project, I build a mini honeynet in Azure and ingest log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, apply some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

<br />

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/x6UdJrr.png)

<br />

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/l91mgkr.png)

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

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

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-04-09 09:45 EST
Stop Time 2023-04-10 09:45 EST

| Metric                                          | Count
| ----------------------------------------------- | -----
| SecurityEvents (Windows VMs)                    | 33,456
| Syslog (Linux VM)                               | 7789
| SecurityAlert (Microsoft Defender for Cloud)    | 14
| SecurityIncident (Sentinel Incidents)           | 295
| NSG Inbound Malicious Flows Allowed             | 619

<br />

## Attack Maps AFTER Hardening / Security Controls

![NSG Allowed Inbound Malicious Flows](https://i.imgur.com/BP8DuH3l.png)

```The remaining map queries returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

<br />

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-04-10 11:00 EST
Stop Time	2023-04-11 11:00 EST

| Metric                                          | Count
| ----------------------------------------------- | -----
| SecurityEvents (Windows VMs)                    | 10,519
| Syslog (Linux VM)                               | 34
| SecurityAlert (Microsoft Defender for Cloud)    | 0
| SecurityIncident (Sentinel Incidents)           | 14
| NSG Inbound Malicious Flows Allowed             | 44

<br />

## Overall Improvement

| Metric                                          | Count
| ----------------------------------------------- | -----
| SecurityEvents (Windows VMs)                    | -68.56%
| Syslog (Linux VM)                               | -99.56%
| SecurityAlert (Microsoft Defender for Cloud)    | -100%
| SecurityIncident (Sentinel Incidents)           | -95.25%
| NSG Inbound Malicious Flows Allowed             | -92.89%

![48 Hour Improvement](https://i.imgur.com/eUzTyCD.png)


<br />

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastially reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
