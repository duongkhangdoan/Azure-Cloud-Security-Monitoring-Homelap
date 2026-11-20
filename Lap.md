# Azure Cloud Security & Monitoring Homelab

**Microsoft Azure | Ubuntu Server | Virtual Network | NSG | Azure Monitor | Log Analytics | KQL | Azure RBAC | Managed Identity | Azure CLI**

## Project Overview

This hands-on cybersecurity homelab demonstrates foundational Azure cloud security controls, Linux log monitoring, SSH threat detection, automated alerting, and identity-based least-privilege access. I deployed an Ubuntu virtual machine in Microsoft Azure, collected its Linux system and authentication logs into a Log Analytics workspace, analyzed suspicious SSH activity with Kusto Query Language (KQL), configured an Azure Monitor alert with email and SMS notifications, and validated Azure RBAC permissions using a system-assigned managed identity.

**Environment:** Azure for Students subscription  
**Resource group:** `rg-soc-security-lab`  
**Ubuntu VM:** `vm-ubuntu-security`  
**Log Analytics workspace:** `law-soc-security-lab`  
**Observed VM region:** `japanwest`  
**Observed VM size:** `Standard_B2ats_v2`

> **Scope:** This is a controlled educational lab. SSH test activity was generated against my own Azure VM. The exercise demonstrates detection of repeated invalid SSH usernames, not proof of a successful compromise or an external attacker.

<img width="1066" height="400" alt="image" src="https://github.com/user-attachments/assets/d1a9560a-92b7-4522-8e68-fdd7525045ee" />
<img width="747" height="810" alt="image" src="https://github.com/user-attachments/assets/a59bf927-f2e4-4142-81eb-a438f6d97b9b" />
<img width="873" height="930" alt="image" src="https://github.com/user-attachments/assets/48763065-0620-45c6-bbd7-7299f61dd49e" />
<img width="859" height="569" alt="image" src="https://github.com/user-attachments/assets/f136fe31-bfd1-4e9a-8054-89486ae4db01" />
<img width="773" height="1136" alt="image" src="https://github.com/user-attachments/assets/c8195aa5-ac46-4eb9-8a44-0edd3d314618" />
<img width="734" height="1372" alt="image" src="https://github.com/user-attachments/assets/dd4e8b3d-2f29-4f2e-ab71-98506c8f7224" />
<img width="644" height="1110" alt="image" src="https://github.com/user-attachments/assets/76725677-2300-431f-b0aa-0116a20bedeb" />
<img width="871" height="722" alt="image" src="https://github.com/user-attachments/assets/dfaf84c9-7670-438b-8828-9ed549b39d8c" />
<img width="670" height="1266" alt="image" src="https://github.com/user-attachments/assets/5a4f888c-6d83-442a-b890-626bc7a3fea3" />
<img width="876" height="625" alt="image" src="https://github.com/user-attachments/assets/61f44094-cc6f-44c8-b090-cdb0ba4a424c" />
<img width="761" height="938" alt="image" src="https://github.com/user-attachments/assets/3fcda40c-fa25-4e72-bb4b-0ea57979f32c" />
<img width="975" height="456" alt="image" src="https://github.com/user-attachments/assets/f34469a9-8cd8-4164-99a5-fdfaba7be1c5" />


## Objectives

- Deploy a Linux workload in Azure with foundational network security controls.
- Centralize Linux security logs for investigation using Azure Monitor and Log Analytics.
- Write KQL queries to identify suspicious SSH authentication activity.
- Configure and validate an automated alert and notification workflow.
- Apply Azure RBAC to a system-assigned managed identity using least-privilege access.
- Verify both authorized read access and denied administrative access through Azure CLI.

## Architecture

```text
                 Windows workstation
                          |
                   SSH test traffic
                          |
                          v
                 Azure Virtual Network
                          |
                    Subnet + NSG
                          |
                          v
                  Ubuntu Azure VM
                vm-ubuntu-security
                    |           |
       Linux syslog/auth logs     System-assigned managed identity
                    |           |
                    v           v
           Azure Monitor     Azure Resource Manager
                    |           |
                    v           +-- Reader on VM: read allowed
          Log Analytics         +-- VM start action: denied
        law-soc-security-lab
                    |
                    v
              KQL detection
                    |
                    v
           Azure Monitor alert rule
          SSH-BruteForce-Detection
                    |
                    v
             Action group
          ag-ssh-security-alert
                    |
              Email + SMS
```

The diagram represents the logical workflow, not a packet-level network diagram. Specific subnet addresses, NSG rules, and data collection rule configuration should be documented from the Azure Portal before adding exact values.

## Technologies Used

| Component | Purpose |
|---|---|
| Microsoft Azure | Cloud infrastructure and resource management |
| Ubuntu Server | Linux workload and SSH authentication log source |
| Azure Virtual Network / Subnet | Network isolation and connectivity |
| Network Security Group (NSG) | Network traffic filtering |
| Azure Monitor | Monitoring and alert evaluation |
| Log Analytics workspace | Centralized log storage and querying |
| Kusto Query Language (KQL) | Security log analysis and detection logic |
| Azure RBAC | Role-based authorization for Azure resources |
| System-assigned managed identity | Azure authentication without storing credentials in application code |
| Azure CLI | Identity authentication and authorization verification |
| Azure Monitor action group | Email and SMS alert notifications |

## Implementation

### 1. Azure Infrastructure and Network Security

I deployed an Ubuntu VM named `vm-ubuntu-security` in the `rg-soc-security-lab` resource group and configured its Azure networking using a virtual network, subnet, and Network Security Group (NSG). I used SSH to administer the Linux VM and to generate controlled authentication events for security monitoring.

**Security concepts practiced:** cloud resource organization, network segmentation, network access controls, and secure remote administration.

> Add the actual VNet name, subnet CIDR, and NSG inbound/outbound rules after checking them in Azure. Do not publish private credentials, SSH private keys, or sensitive access information.

<img width="857" height="463" alt="image" src="https://github.com/user-attachments/assets/4b694e78-8262-4d7a-8861-dc42a3320bff" />
<img width="734" height="1350" alt="image" src="https://github.com/user-attachments/assets/3d546f5d-70e3-4f2b-965a-f730961fb54e" />


### 2. Centralized Linux Log Collection

I connected Linux logging to Azure Monitor and the Log Analytics workspace `law-soc-security-lab`. I confirmed that the `Syslog` table received events from `vm-ubuntu-security`, including `sshd`, `systemd`, and `rsyslog` messages.

<img width="733" height="674" alt="image" src="https://github.com/user-attachments/assets/34e090b1-938c-4920-a16b-0503310f94a9" />
<img width="975" height="294" alt="image" src="https://github.com/user-attachments/assets/c2c99a21-cecf-4cb7-ab6f-a0382138758c" />
<img width="975" height="248" alt="image" src="https://github.com/user-attachments/assets/33020e2b-fa2c-4b86-bad1-d365da943716" />
<img width="903" height="559" alt="image" src="https://github.com/user-attachments/assets/0cd27489-911f-4eac-87a2-13836ee83a56" />
<img width="767" height="602" alt="image" src="https://github.com/user-attachments/assets/5ccb5776-094f-4c1a-a562-793fe7fd86b3" />
<img width="975" height="322" alt="image" src="https://github.com/user-attachments/assets/63d13b10-2638-457f-bf6e-e6e2a25fdb17" />
<img width="975" height="274" alt="image" src="https://github.com/user-attachments/assets/875af10d-16a6-4f40-94c4-a58cbda378eb" />


Initial verification query:

```kusto
Syslog
| where TimeGenerated > ago(6h)
| take 50
```
<img width="975" height="274" alt="image" src="https://github.com/user-attachments/assets/0fd09c2a-7d09-47ba-8598-085565a73d73" />


To inspect SSH-related messages:

```kusto
Syslog
| where TimeGenerated > ago(24h)
| where ProcessName == "sshd" or SyslogMessage contains "sshd"
| project TimeGenerated, Computer, Facility, ProcessName, SyslogMessage
| order by TimeGenerated desc
```

**Observed result:** Azure Log Analytics returned SSH session and authentication-related messages, including accepted public-key logins and invalid username attempts.

### 3. SSH Threat Detection Using KQL

I generated multiple controlled SSH login attempts using invalid usernames from my Windows workstation against my Azure Ubuntu VM. The SSH client returned `Permission denied (publickey)` and Ubuntu recorded events such as `Invalid user ... from <source IP>`.

To inspect recent invalid SSH usernames:

```kusto
Syslog
| where TimeGenerated > ago(1h)
| where ProcessName == "sshd"
| where SyslogMessage contains "Invalid user"
| project TimeGenerated, Computer, Facility, ProcessName, SyslogMessage
| order by TimeGenerated desc
```
<img width="633" height="188" alt="image" src="https://github.com/user-attachments/assets/1f4ef2c2-b106-41eb-89f4-f2d919fd1208" />
<img width="975" height="207" alt="image" src="https://github.com/user-attachments/assets/dc013d31-d378-4ec7-8657-1e4dbfa5c632" />
<img width="975" height="450" alt="image" src="https://github.com/user-attachments/assets/5e3885dd-3b24-4097-ac67-23a4b35e741f" />

For a grouped detection, I used this KQL query:

```kusto
Syslog
| where TimeGenerated > ago(15m)
| where ProcessName == "sshd"
| where SyslogMessage startswith "Invalid user"
| extend SourceIP = extract(@"from\s+(\d{1,3}(?:\.\d{1,3}){3})", 1, SyslogMessage)
| where isnotempty(SourceIP)
| summarize FailedAttempts = count() by SourceIP
| where FailedAttempts >= 5
```

<img width="975" height="488" alt="image" src="https://github.com/user-attachments/assets/b9602ddd-f06d-409c-be3f-b7d554b45758" />

**Detection logic:** Return a row when five or more `Invalid user` SSH events from the same source IP occur within the previous 15 minutes.

**Observed result:** A test query over a 30-minute window returned **6 invalid-user events from one source IP**. This demonstrated that the log collection, parsing, and aggregation logic worked.

**Important limitation:** These are invalid-username events, not necessarily five completed password authentication failures. The rule detects a pattern consistent with SSH username probing or brute-force attempts; it does not establish attacker intent by itself.

### 4. Automated Alerting and Notifications

I created an Azure Monitor log-search alert rule using the KQL detection query.

| Setting | Lab configuration |
|---|---|
| Alert rule | `SSH-BruteForce-Detection` |
| Signal | Custom log search |
| Query type | Aggregated logs |
| Detection window | 15 minutes in KQL |
| Detection threshold | `FailedAttempts >= 5` per source IP |
| Measure | Table rows |
| Alert condition | Table rows > 0 |
| Evaluation frequency | 5 minutes |
| Severity | 2 - Warning |
| Action group | `ag-ssh-security-alert` |
| Notifications | Email and SMS |

The action group was configured to notify me by email and SMS when the alert fired. After generating the test events, I confirmed that Azure Monitor displayed the alert in a **Fired** state and received a notification.

<img width="719" height="1184" alt="image" src="https://github.com/user-attachments/assets/1cf302c8-46b2-4d36-9c2b-7f695473dd6e" />
<img width="868" height="471" alt="image" src="https://github.com/user-attachments/assets/541b981f-6e41-415e-9eed-9bcc16f37244" />
<img width="785" height="277" alt="image" src="https://github.com/user-attachments/assets/724aea93-f7d7-4222-b720-932701d778e9" />
<img width="831" height="366" alt="image" src="https://github.com/user-attachments/assets/7c21ded5-0a52-486e-a6d4-d00bb620120d" />
<img width="867" height="212" alt="image" src="https://github.com/user-attachments/assets/f8c9d4d9-a6a8-4961-9b5b-8ad8ec93f66f" />


**Detection workflow:**

1. Generate controlled invalid SSH username attempts against the lab VM.
2. Verify corresponding `sshd` entries locally with `journalctl`.
3. Confirm ingestion into the Log Analytics `Syslog` table.
4. Execute the KQL query to identify repeated events from one source IP.
5. Confirm that Azure Monitor evaluates the query and fires the alert.
6. Verify delivery of email/SMS notifications.

Example local verification:

```bash
sudo journalctl -u ssh --since "15 minutes ago" --no-pager | grep "Invalid user"
```

<img width="674" height="561" alt="image" src="https://github.com/user-attachments/assets/e207fc26-98c5-4d41-b9be-b1f94b40efe0" />
<img width="975" height="144" alt="image" src="https://github.com/user-attachments/assets/ce30c42e-f13a-47ee-b342-34cc4b95c2d4" />

> The precise time window and frequency depend on the alert configuration and log ingestion latency. A threshold of five events in a 15-minute query window does not mean that every set of five events will always be detected immediately.

<img width="803" height="315" alt="image" src="https://github.com/user-attachments/assets/c47a4eed-6999-46e0-b6ec-d43b59097c0d" />
<img width="812" height="311" alt="image" src="https://github.com/user-attachments/assets/57b12289-0021-4201-ba15-dee44f363872" />
<img width="540" height="692" alt="image" src="https://github.com/user-attachments/assets/61c1bef8-0fad-4b25-b3ed-9f9a86ba3fc9" />

### 5. Azure RBAC and Managed Identity

I enabled a **system-assigned managed identity** on `vm-ubuntu-security` and assigned it the built-in **Reader** role scoped to that VM. This follows the principle of least privilege: the identity can read information about the assigned Azure resource but cannot perform administrative actions such as starting the VM.

| Identity | Role | Scope | Purpose |
|---|---|---|---|
| My Azure user account | Owner (inherited from subscription) | Subscription | Manage lab resources and access |
| `vm-ubuntu-security` managed identity | Reader | This VM | Read Azure resource metadata without broader management rights |

<img width="975" height="442" alt="image" src="https://github.com/user-attachments/assets/9c6482a4-129d-4950-9c23-e2c0787b0631" />

I installed Azure CLI on the Ubuntu VM and authenticated using the managed identity:

```bash
az --version
az login --identity
```

The login succeeded with a `systemAssignedIdentity` service principal. No Azure account password was entered into the CLI for this authentication flow.

**Verify authorized read access:**

```bash
az vm show \
  --resource-group rg-soc-security-lab \
  --name vm-ubuntu-security \
  --query "{Name:name, Location:location, VMSize:hardwareProfile.vmSize}" \
  --output table
```

Observed output:

```text
Name                Location   VMSize
------------------  ---------  -----------------
vm-ubuntu-security  japanwest  Standard_B2ats_v2
```

**Verify that an administrative operation is denied:**

During the controlled test, I submitted an Azure Resource Manager POST request to the VM `start` endpoint while authenticated as the Reader managed identity. Azure returned:

```text
AuthorizationFailed
... does not have authorization to perform action
'Microsoft.Compute/virtualMachines/start/action' ...
```

<img width="698" height="501" alt="image" src="https://github.com/user-attachments/assets/71c33b71-e9d7-4d52-8ffc-c959e8b04788" />
<img width="783" height="150" alt="image" src="https://github.com/user-attachments/assets/686c855d-4319-4871-b3f0-97cb68c51900" />

The operation was denied; the VM was not started by that request. This provided a practical authorization check: the managed identity could **read** VM details but could not **start** the VM.

> **Safety note:** An ARM `POST` request to a VM `start` endpoint is a real management operation, **not** a dry run. It was denied in this test because the identity lacked the required permission. Do not repeat it with an Owner or Contributor identity unless you intend to start the VM.

**Key distinction:** Azure RBAC controls access to Azure management-plane resources. It does not by itself reduce the Linux permissions of the `azureuser` account used to SSH into the VM.

## Results and Validation

| Test | Expected outcome | Observed outcome |
|---|---|---|
| Query Linux logs in Log Analytics | Syslog events are visible | Passed |
| Inspect SSH authentication activity | `sshd` messages are visible | Passed |
| Generate invalid SSH usernames | `Invalid user` messages are logged | Passed |
| Aggregate suspicious SSH activity in KQL | Count events per source IP | Passed: 6 events in a 30-minute test window |
| Configure automated detection | Alert rule enabled | Passed |
| Trigger the alert | Azure Monitor reports Fired | Passed |
| Deliver alert notifications | Email/SMS notification | Received notification |
| Authenticate with managed identity | `az login --identity` succeeds | Passed |
| Read VM metadata with Reader | `az vm show` succeeds | Passed |
| Attempt VM start with Reader | Authorization denied | Passed: `AuthorizationFailed` |

## Security Concepts Demonstrated

- **Centralized logging:** Investigating Linux host activity from a cloud log analytics platform.
- **Detection engineering:** Converting a recognizable log pattern into a repeatable KQL query and alert condition.
- **Security monitoring:** Identifying potentially suspicious SSH username probing activity.
- **Alerting and notification:** Linking a detection rule to an action group for analyst awareness.
- **Identity and access management:** Authenticating an Azure workload using a managed identity.
- **Least privilege:** Restricting an identity to the Reader role at the VM scope.
- **Authorization validation:** Confirming both successful reads and denied management operations.

## Challenges and Lessons Learned

**Finding SSH logs:** Early SSH queries returned no matching results. I verified SSH session records locally with `journalctl`, checked the `Syslog` table, adjusted the query time range, and filtered for `sshd` and `Invalid user` messages.

**Understanding authentication events:** A rejected SSH login using an invalid username produced an `Invalid user` record. This is different from a completed password failure, so the alert is described as an invalid-username detection rather than a definitive password brute-force detection.

**Validating RBAC:** Successfully assigning a role in the portal was not sufficient evidence by itself. I authenticated from the VM using its managed identity, confirmed that `az vm show` worked, and observed an `AuthorizationFailed` response when the identity attempted a privileged VM start operation.

**Cloud operations:** Azure resource management, Linux host permissions, network controls, and monitoring permissions operate at different layers. Testing each layer separately helps avoid incorrect assumptions about what a role or rule protects.

## Screenshots

Add sanitized screenshots to the `screenshots/` directory and update the links below. Recommended evidence:

| File | What it should show |
|---|---|
| `01-azure-vm-overview.png` | Ubuntu VM resource overview (hide sensitive details) |
| `02-syslog-ingestion.png` | Log Analytics `Syslog` results |
| `03-ssh-invalid-user-events.png` | `Invalid user` events in KQL results |
| `04-kql-detection-results.png` | Grouped source IP and event count |
| `05-alert-rule.png` | Enabled `SSH-BruteForce-Detection` rule |
| `06-alert-fired.png` | Azure Monitor alert in Fired state |
| `07-notification.png` | Redacted email or SMS alert notification |
| `08-managed-identity.png` | Enabled system-assigned identity |
| `09-reader-role-assignment.png` | Reader role assigned to VM identity |
| `10-cli-read-success.png` | Successful `az vm show` output |
| `11-cli-authorization-denied.png` | `AuthorizationFailed` response |

**Before publishing:** Blur or crop personal email addresses, phone numbers, tenant/subscription/object IDs, public IP addresses when unnecessary, SSH key fingerprints, session identifiers, and any secrets. Never upload private SSH keys, access tokens, or credential files.

## Future Improvements (Not Yet Implemented)

- Ingest and investigate Azure Activity Logs for control-plane operations and role assignment changes.
- Expand detections to distinguish invalid usernames, failed passwords, and successful SSH authentication.
- Tune alert thresholds and add false-positive handling.
- Add documented NSG rule reviews and secure administration recommendations.
- Build an Azure Workbook dashboard for security monitoring.
- Explore Microsoft Sentinel if licensing and student subscription limits allow.

## Resume Summary

> Deployed and secured an Ubuntu VM in Azure using Virtual Networks, subnets, and Network Security Groups (NSGs). Implemented Azure RBAC and managed identity to enforce least-privilege access, validating permissions using Azure CLI. Analyzed Linux security logs using KQL to detect suspicious SSH activity and configured Azure Monitor alerts with automated email and SMS notifications.

## Disclaimer

This project was completed in a personal educational Azure lab. All security tests were performed against infrastructure under my control. The examples are for defensive security learning and do not represent a production deployment.
