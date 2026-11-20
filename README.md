# Azure Cloud Security & Monitoring Homelab

**Level:** Beginner–Intermediate  
**Focus:** Cloud Security | SOC Monitoring | Identity & Access Management (IAM)  
**Platform:** Microsoft Azure (Azure for Students)

## Project Overview

This hands-on homelab introduces core cloud security and Security Operations Center (SOC) practices using Microsoft Azure. The project involves deploying and securing an Ubuntu virtual machine, collecting Linux security logs, detecting suspicious SSH authentication activity with Kusto Query Language (KQL), configuring automated alerts, and validating least-privilege access through Azure RBAC and a system-assigned managed identity.

All simulated SSH activity is performed against a VM owned and controlled by the learner.

## What You Will Learn

- **Azure fundamentals:** Organize resources and deploy a Linux VM in the cloud.
- **Network security:** Understand virtual networks, subnets, and Network Security Groups (NSGs).
- **Security monitoring:** Collect and investigate Linux Syslog events in Azure Monitor and Log Analytics.
- **Threat detection:** Use KQL to identify repeated invalid SSH usernames.
- **Alerting:** Configure Azure Monitor alerts and email/SMS notifications.
- **Cloud IAM:** Apply Azure RBAC, managed identities, and the principle of least privilege.
- **Access validation:** Use Azure CLI to confirm allowed read operations and denied administrative operations.

## Hands-on Exercises

1. Deploy an Ubuntu Server VM with Azure VNet, subnet, and NSG controls.
2. Send Linux Syslog and SSH authentication events to a Log Analytics workspace.
3. Generate controlled invalid SSH username attempts against your own VM.
4. Write KQL queries to investigate SSH activity and count attempts by source IP.
5. Create an alert for **5 or more invalid SSH username events from the same source IP within 15 minutes**, and configure email/SMS notifications.
6. Enable a system-assigned managed identity, assign it the **Reader** role at VM scope, and verify its permissions using Azure CLI.

## Prerequisites

**Required**
- An active Microsoft Azure subscription (such as Azure for Students), with permission to create the necessary resources.
- A Windows, macOS, or Linux computer with an SSH client and internet access.
- Basic familiarity with Linux commands, SSH, IP addresses, ports, and firewalls.
- An SSH key or another secure, supported authentication method for your own Ubuntu VM.
- Enough available Azure credit or budget for VM runtime, monitoring, log ingestion, and alerting costs.

**Helpful, but not required**
- Basic understanding of security logs, authentication events, and SOC workflows.
- Familiarity with KQL, Azure CLI, and role-based access control.

> **Cost and safety:** Azure services and some alert notification channels may incur charges. Review current pricing and configure budgets. Restrict SSH access to trusted IPs where possible; never publish passwords, SSH private keys, access tokens, or sensitive account information. Stop or deallocate unused compute resources when appropriate.

## Expected Outcome

By the end of the lab, you will have a small Azure security monitoring environment that collects Linux security logs, detects suspicious SSH authentication patterns, triggers alerts, and demonstrates least-privilege access using Azure RBAC and managed identity.

**Skills demonstrated:** Azure networking, Linux security monitoring, KQL, threat detection, alerting, RBAC, managed identity, and Azure CLI.


