# CloudHealth Prerequisites

- Table of Contents
{:toc}

# Changelog

| Date       | Author           | Issue    | Description     |
| ---------- | ---------------- | -------- | --------------- |
| 10.06.2021 | Shyjin Varaprath | VCS 2067 | Initial Version |

## Introduction

### Purpose

Arrange the pre-requisites before deploying and setting up the VMware CloudHealth component of VCS.

### Audience

- VCS Engineers

### Scope

There are a sequence of steps as mentioned in the CloudHealth LLD that are distributed between the CloudHealth / CFM Team and VCS team. This work instruction is intended to cover below listed tasks and activities for Cloud Health that fall under the scope of the VCS Team:

1. Create the VCS Active Directory service account for each customer / tenant.
2. Assign the read-only permissions on the vCenter server (resource pools / Cluster) to the service account.
3. Deploy & configure the aggregator VM using VCS Manage phase playbook.

# Related Documents

This document is a subset of Atos Technology Lifecycle Management (ATLM) artefacts.

| Document                  | Document Name                                                |
| ------------------------- | ------------------------------------------------------------ |
| VMware Cloud Services: HLD | [VMware Cloud Services: HLD](../design/hldDigitalHybridCloud.md) |
| VCS Infrastructure: LLD   | [VCS Infrastructure: LLD](../design/lldInfrastructure.md) |
| CloudHealth: LLD          | [CloudHealth: LLD](../design/lldCloudHealth.md) |
| Naming Convention         | [Naming Convention](../design/namingConvention.md) |

# Infrastructure Requirements

1. A readily deployed VCS Infrastructure instance
2. Knowledge of VCS RBAC design.

# Assumptions

It is assumed that the engineer following this work instruction is well aware of the VCS management stack components and can identify the correct management servers referred to in this document without any issues.

# VCS tasks for CloudHealth

## Create the VCS Active Directory service account

There should be a dedicated service account for the Cloud Health component. This service account will be used by the CloudHealth "VMware Datacenter account" to connect to and access the target vCenter inventory.

The steps to create the service account are:

1. From the Ansible server (ans001)  run the service account creation playbook in the "manage" folder.

   ```shell
   @ans001:~/dhc/manage$ansible-playbook createServiceAccount.yml
   ```

2. Provide the user inputs as requested. Do note that the service name "cht" would remain as it is to identify it as a service account used for CloudHealth. The account number would depend upon the sequence of already existing service accounts for CloudHealth (in case of a multi-tenant VCS environment). If there are none, then indeed it would be 01 to start with.

   The resource group name would also reflect the customer code and the target component (in this case the vCenter server) on
   which we need to grant access on. Do refer to the active directory console for the existing resource group names.

   ```yaml
   Enter domain username in format dasId@domain.next: <yourdomainaccount>
   Enter the password for the domain user. Please note that the password you enter will not be displayed on the screen.
   Password: <yourpassword>
   Enter service name for service account in 3 Alphabets e.g. mid/git/adc: cht
   Enter service account number in 2 digits number e.g. 01/02/03: <01>
   Enter group name which you want to add  in format - rsce-gre22-vcs-l-readonly
     If you want to add multiple groups please add group comma separated in format - rsce-gre22-vcs-l-readonly,rsce-dhc-vop-l-admins
    If you do not want additional group, please press enter [rsce-dhc-ad-g-adminpwdpolicy]: rsce-<customercode>-vcs-l-readonly
   ```

3. After the playbook is executed, the service account would be created in the format svc-< customercode >-cht< xx >. And this would also be added to the resource group for vCenter access. The credentials for this service account would be auto generated and stored in the Hashi vault.

4. The credentials of this service account should be conveyed to the CloudHealth / CFM team for this would be used while creating the "VMware Datacenter Account" in the CloudHealth console.

5. In a multi-tenant model, we will create one service account per tenant.

## Assign the read-only permissions on the vCenter server (resource pools / Cluster)

The service account created in the earlier step is granted read-only privileges on the entire vCenter inventory hierarchy. However to comply with the multi-tenant design, the permission hierarchy should be broken and assigned to individual components and specific "resource groups" (resource groups would be considered as the isolation boundary for a tenant in a multi-tenant architecture in VCS).

The permissions for the service account on the vCenter components should be assigned as follows.

1. On the vCenter object.

2. On the Datacenter object.

3. On the Cluster object.

4. On the ESXi hosts.

5. On the vSAN datastore object

   **For all of the above constructs use the following permission setting**

   | User/Group                       | Role      | Defined In  |
   | -------------------------------- | --------- | ----------- |
   | domain\rsce-\<customercode\>-vcs-l-readonly | Read-only | This Object |

6. On the Resource group

   | User/Group                       | Role      | Defined In                   |
   | -------------------------------- | --------- | ---------------------------- |
   | domain\rsce-\<customercode\>-vcs-l-readonly | Read-only | This Object and its children |

## Deploy & configure the aggregator VM using VCS Manage phase playbook

As part of the Cloud Health implementation, we need to deploy a collector VM at the VCS end that would collect the inventory & other data structures from the vCenter and pass it to the Cloud Health SaaS console.

This VM is referred to as the CloudHealth Aggregator VM. To deploy and configure this VM, you should obtain the CloudHealth Aggregator token (* refer to the CloudHealth LLD for details) and use it as an input to the playbook.

The steps to install & configure the Cloud Health component at VCS is as follows:

1. From the Ansible server (ans001)  run the createCloudHealth playbook in the "manage" folder. Before executing the playbook do note that it requires creation of the input file chtVars.yml (* refer to the detailed description of the playbook yml file).

   ```shell
   @ans001:~/dhc/manage$ansible-playbook createCloudHealth.yml
   ```

2. Provide the user inputs as requested. The Aggregator installer token is the one which you would obtain from the CloudHealth / CFM team.

   ```config
   Please note that this automation requires input file with specific variables in /home/AXXXX directory
   - chtVars.yml
   All required information You can find on Git in readme file of role dhc-createCloudHealth.Please refer to the code repository  
   path ../manage/roles//dhc-createCloudHealth/README.md
   --------------------------------------------------------------------------------------------------
   Please confirm that you have read the documentation, prepared the required input file,placed it in mentioned path and
   You are willing to continue the script execution.
   Confirmation (yes)
   : yes
   Enter domain username in format dasId@domain.next: <yourdomainaccount>
   Enter the password for the domain user. Please note that the password you enter will not be displayed on the screen.
   Password:<yourpassword>
   Please Provide CloudHealth Aggregator VM installer token: abcdefghijklmnopqrstuvwxyz
   
   ```

3. After the playbook is executed, the CloudHealth Aggregator VM is deployed and configured. Along with other relevant settings (* refer to the detailed description within the playbook yml file).

4. Once the aggregator VM is deployed successfully, contact the CloudHealth / CFM team to inform them about the same and confirm whether the VMware Datacenter Account in CloudHealth portal shows as healthy & has started collecting data.

# Appendix

   | Team                             | Contact Details |
   | -------------------------------- | --------------- |
   | CloudHealth / Cloud Financials Management  | `Sjak.Verlangen@atos.net` & `mircea-vlad.pelau@atos.net` |
