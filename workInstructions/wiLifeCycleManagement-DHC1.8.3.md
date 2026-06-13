# Table of Contents

- [Table of Contents](#table-of-contents)
- [Title: Lifecycle Management - 1.8.3](#title-lifecycle-management---183)
- [List of Changes](#list-of-changes)
- [Introduction](#introduction)
- [Scope](#scope)
- [Related Documents](#related-documents)
- [LCM code update (Clone Repository)](#lcm-code-update-clone-repository)
- [Windows Components upgrade to 2022](#windows-components-upgrade-to-2022)
  - [InPlace TSS upgrade to 2022](#in-place-upgrade-of-tss-servers)
  - [AD Server upgrade to 2022](#active-directory)
  - [WSUS server upgrade to 2022](#wsus)
  - [ICA and RCA server upgrade to 2022](#ica-and-rca)
- [Upcoming Playbooks Execution Instructions](#upcoming-playbooks-execution-instructions)
  - [Assigning Backup Tags](#assigning-backup-tags)
  - [vROPS Agent installation](#vrops-agent-installation)
  - [Known Issues](#known-issues)
  - [VRLI Agent Installation](#vrli-agent-installation)
  - [Alcatraz scanner Installation](#alcatraz-scanner-installation)
  - [Trend Micro Anti Virus Installation](#trend-micro-anti-virus-installation)
  -[Removal of Elevated access on svc-{{ locationCode }}-ans01](#removal-of-elevated-access-on-svc--locationcode--ans01)

## Title: Lifecycle Management - 1.8.3

## List of Changes

| Date       | Issue    | Author          | TOS  | Description |
| ---------- | -------- | --------------- | ---- | ---------------------- |
| 30/07/2024 | VCS-13481 | Krishnasai Dandanayak |      | Upgrade 2022 windows  |
| 08/10/2024 | VCS-14059 | Divyaprakash J |      | Document Update for Antivirus Agent installation |
| 05/02/2025 | VCS-14999 | Krishnasai Dandanayak | |Adjusted the Document flow and added the windows components WI links under each component upgrade |
| 24/02/2025 | VCS-14999 | Krishnasai Dandanayak | |Adjusted the Document flow for ADC servers |
| 21/10/2025 | VCS-17043 | Krishnasai Dandanayak | |Added Backup Tags playbook |

## Introduction

This page describes Life Cycle Management of DHC components. Some DHC components can be upgraded independently, others have to follow the exact order.

## Scope

The work instruction is intended to cover below tasks:

- LCM code update
- Upgrade of non-VCF components
- Post LCM Validation

## Related Documents

| Document |
| -------- |
| [DHC 1.8 - wiLifeCycleManagement](wiLifeCycleManagement-DHC1.8.md) |
| [InPlace TSS upgrade to 2022](wiInPlaceUpgradeTSSto2022.md) |
| [AD Server upgrade to 2022](wiUpgradeADCto2k22.md) |
| [WSUS server upgrade to 2022](wiUpgradeWSUSto2k22.md) |
| [ICA and RCA server upgrade to 2022](wiUpgradeICAandRCAto2k22.md) |

## LCM code update (Clone Repository)

Please use following steps to clone DHC repository. Execute all commands as *domain user* logged on Ansible Host. Change:

```bash
axxxxx@ans001:~$ cd
```

Configure git and clone DHC repository:

```bash
git config --global user.name 'User Name' && git config --global user.email 'email.address@atos.net'
git config --global core.hookspath 'hooks' && git config --global credential.helper store && git config --global submodule.recurse true
git config --global http.proxy 'http://< locationCode >pxy001.< customerCode >dhc01.next:3128'
git clone https://github.com/GLB-CES-PrivateCloud/dhc.git --recurse-submodules
cd DHC
git tag
git checkout DHC-1.8.3-20250127 ## Kindly check the latest release date as tag
git submodule update --init update
cd update  ## now we can see the all playbooks under the update branch
```

## non-VCF components

LCM DHC 1.8.3 proces is related with upgrade from windows 2016 to windows 2023. Proceed with steps in order described below.

### Download Binaries and Create Template

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook importingTemplateForWindows.yml
```

#### Windows Components upgrade to 2022

After Successfully completing the pre-upgrade steps and the below is the sequence:
    - TSS
    - Active Directory (Initial adc002 and then adc001)
    - WSUS
    - ICA and RCA

#### In-Place upgrade of TSS servers

Execute the following Ansible Playbook for downloading the ISO imaage and attached to the VM's. Then follow the instructions mentioned over the WI to perform the in-place upgrade activity.

```bash
ansible-playbook upgradeTssTo2022.yml
```

For reference, Please find the below link for document.
[InPlace TSS upgrade to 2022](wiInPlaceUpgradeTSSto2022.md)

##### Active Directory

>[!NOTE]
> **This activity should perform in the out of business hours**
>
> **Below are the following steps for ADC Upgrade**
>
> - First, fully demote the adc002 server. Check vCenter to confirm that the server name has the suffix "-old" and that it is in a powered-off state.
> - Fully promote the adc002 server and perform the necessary validation steps.
> - Once the promotion and validation of adc002 are completed, proceed to fully demote the adc001 server. Verify in vCenter that the server name has the "-old" suffix and that it is powered off.
> - Fully promote the adc001 server and follow the validation procedures.

By executing the following playbook to demote the AD server

```bash
ansible-playbook demoteAdDomainController.yml
```

Execute the following Ansible Playbook for upgrading the both active directory servers. Initially it need to be select as adc002 then after finishing the adc002, Need to perform on adc001.

```bash
ansible-playbook upgradeADto2k22.yml
```

For reference, Please find the below link for document. [AD Server upgrade to 2022](wiUpgradeADCto2k22.md)

##### WSUS

Execute the following playbook which it covers snapshot

```bash
ansible-playbook demoteWsusServer.yml
```

Execute the following Ansible Playbook for upgrading the WSUS server and confirguring the patching on the components.

```bash
ansible-playbook upgradeWsusTo2k22.yml
```

For reference, Please find the below link for document.  [WSUS server upgrade to 2022](wiUpgradeWSUSto2k22.md)

#### ICA and RCA

Execute the following playbook which it covers snapshot

```bash
ansible-playbook demoteCaServer.yml
```

IMPORTANT NOTE: Make sure the playbook finished successfully before moving on to the next step.

Execute the following Ansible Playbook for upgrading the ICA and RCA servers.

```bash
ansible-playbook upgradeICAandRCAto2k22.yml
```

For reference, Please find the below link for document.  [ICA and RCA server upgrade to 2022](wiUpgradeICAandRCAto2k22.md)

#### Upcoming Playbooks Execution Instructions

- These playbooks should only be executed on the following servers: adc001, adc002, wus001, ica001.
- Reason: TSS servers are undergoing an in-place upgrade, and the RCA server will be in a powered-off state.
- Mandatory Extra Vars: Each playbook requires the HOSTS variable to specify the hostname or group of hosts based on the manage/hosts inventory file.
- Example Usage: To execute the playbook on a single host:

```bash
ansible-playbook installAvAgentsforWindows2k22.yml -e "HOSTS=adc002"
```

- To execute the playbook on multiple servers:

```bash
ansible-playbook installAvAgentsforWindows2k22.yml -e "HOSTS=adc001,adc002,wus001,ica001"
```

#### Assigning Backup Tags

- After upgrading the Windows servers to 2022, it is essential to assign backup tags to these servers to ensure they are included in the backup schedules.
- Execute the following Ansible Playbook to assign backup tags to the upgraded Windows servers.
- Provide the backup tag name while it prompts during the playbook execution.

```bash
ansible-playbook addTagsToWindowsVms.yml
```

#### vROPS Agent installation

Execute the following Ansible Playbook for installing vROPS Agent on windows components.
The installation of the vRops Telegraf agent is essential for monitoring purposes. It facilitates the onboarding of systems into vRops, enabling effective post-deployment monitoring and management within the environment.

Using the specified playbook, we have automated the installation of vROps Telegraf Agent installation on the newly deployed windows server.

Mandatory Vars :

- To run the playbook, you need to specify the HOSTS variable to indicate the hosts where you want to install the agent.

```bash
ansible-playbook installTelegrafAgentWindowsfor2k22.yml -e "HOSTS=wus001"
```

#### Known Issues

- The dig lookup requires the python 'dnspython' library and it is not installed

Run the following commands:

```bash
export http_proxy="http://{{ mgmtDns.pxy001.cidr }}.{{ mgmtDns.pxy001.octet }}:{{ proxyPort }}"
export https_proxy="http://{{ mgmtDns.pxy001.cidr }}.{{ mgmtDns.pxy001.octet }}:{{ proxyPort }}"
pip install dnspython
```

- Unable to obtain VROPS token. The password for the user 'loc-vop-telegraf' may have expired. Please reset it via the VROPS UI and try again

- If the playbook runs successfully but Telegraf is not installed correctly, please execute the playbook with the "-vvv" option and check the output of the last task for detailed error information.

**Note:** The Telegraf agent is installed on the new 2022 servers. To monitor the services running on these servers, you need to configure the Telegraf agent and specify the services you want to monitor.

To check more on how to configure service monitoring check document - [wiReplaceVropsEpopsAgentWithTelegrafAgent](wiReplaceVropsEpopsAgentWithTelegrafAgent.md#step-5---configure-custom-service-monitoring)

#### VRLI Agent Installation

Execute the following Ansible Playbook for installing VRLI Agent on windows components.

Mandatory Vars :

- To run the playbook, you need to specify the HOSTS variable to indicate the hosts where you want to install the agent.

```bash
ansible-playbook installVrliAgentsWk22Servers.yml -e "HOSTS=wus001"
```

#### Alcatraz scanner Installation

Execute the following Ansible Playbook for installing Alcatraz on windows components.

Mandatory Vars :

- To run the playbook, you need to specify the HOSTS variable to indicate the hosts where you want to install the agent.

```bash
ansible-playbook installAlcatrazforWindows2k22.yml -e "HOSTS=wus001"
```

#### Trend Micro Anti Virus Installation

- Check the vault whether its has credentails required for antivirus

![AV](images/UpgradeICAandRCAto2k22/AVcreds.png)

- If the credentials is not present in vault , run the following playbook

```bash
ansible-playbook addDeepSecurityToVault.yml
```

- Inputs required : tenantId, token, linuxPolicyId, windowsPolicyId
- Please view the document to get the required details - [DHC IPAM AV](https://msdevopsconfluence.fsc.atos-services.net/confluence/display/DPC/DHC+LAB+AV)
- We can also find it in our group_vars file

Execute the following Ansible Playbook for installing Antivirus Agent on windows components.

Mandatory Vars :

- To run the playbook, you need to specify the HOSTS variable to indicate the hosts where you want to install the agent.

```bash
ansible-playbook installAvAgentsforWindows2k22.yml -e "HOSTS=wus001"
```

#### Removal of Elevated access on svc-{{ locationCode }}-ans01

After AD hardening, we don't have the exact privileged account to use built-in administrator account to perform this activity. So we provided the elevated access to svc-{{ locationCode }}-ans01.

We added the ***Enterprise Admins, Schema Admins, Domain Admins*** roles to the svc-{{ locationCode }}-ans01 account for temporary.

Once the whole windows 2022 upgrade is finished. Please login to the any of the Domain Controller and open the powershell with privileged access and execute the below script to remove the above roles from the ans01 service account.

Here for example we used the gre82, Please change the locationCode as per the requirement and environment.

```bash
$serviceAccount = "svc-gre82-ans01"
$enterpriseAdminsGroup = "Enterprise Admins"
$schemaAdminsGroup = "Schema Admins"
$domainAdminsGroup = "Domain Admins"
Remove-ADGroupMember -Identity $enterpriseAdminsGroup -Members $serviceAccount -Confirm:$false
Remove-ADGroupMember -Identity $schemaAdminsGroup -Members $serviceAccount -Confirm:$false
Remove-ADGroupMember -Identity $domainAdminsGroup -Members $serviceAccount -Confirm:$false
```
