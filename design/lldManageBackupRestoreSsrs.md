# Manage Backup and Restore SSRs Documentation

# Table of Contents

- [Manage Backup and Restore SSRs Documentation](#manage-backup-and-restore-ssrs-documentation)
- [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
- [1. General Information](#1-general-information)
  - [1.1 Purpose](#11-purpose)
  - [1.2 Audience](#12-audience)
  - [1.3 Scope](#13-scope)
  - [1.4 Related documents](#14-related-documents)
  - [1.5 Requirement Levels](#15-requirement-levels)
- [2. Architecture Overview](#2-architecture-overview)
  - [2.1 Business and Solution Requirements](#21-business-and-solution-requirements)
        - [Table 2. Initial Requirements](#table-2-initial-requirements)
  - [2.2 High Level Description](#22-high-level-description)
  - [2.3 Prerequisities and dependencies](#23-prerequisities-and-dependencies)
    - [Assumptions](#assumptions)
    - [VCS prerequisities](#vcs-prerequisities)
    - [License requirements](#license-requirements)
    - [vRO requirements and dependencies](#vro-requirements-and-dependencies)
- [3. Detailed logical design](#3-detailed-logical-design)
  - [3.1 Manage VM Backup Policy SSR](#31-manage-vm-backup-policy-ssr)
    - [Service request overview](#service-request-overview)
    - [Service request inputs](#service-request-inputs)
    - [vRO workflow steps](#vro-workflow-steps)
    - [Inputs](#inputs)
  - [3.2 Backup On-Demand SSR](#32-backup-on-demand-ssr)
    - [Service request overview](#service-request-overview-1)
    - [Service request inputs](#service-request-inputs-1)
    - [vRO workflow steps](#vro-workflow-steps-1)
      - [Inputs](#inputs-1)
  - [3.3 Backup Restore SSR](#33-backup-restore-ssr)
    - [Service request overview](#service-request-overview-2)
    - [Service request inputs](#service-request-inputs-2)
    - [vRO workflow](#vro-workflow)
      - [Steps](#steps)
      - [Inputs](#inputs-2)
  - [3.4 VRO Resources](#34-vro-resources)
    - [Actions](#actions)
    - [Workflows](#workflows)
    - [Configurations](#configurations)
    - [Permissions](#permissions)
- [4. Installation](#4-installation)

# Changelog

|    Date    |   TOS   |   Issue   | Author | Description |
|------------|---------|-----------|--------|-------------|
| 2023-03-03 | VCS 1.7 | CESDHC-5939 | Piotr Lewandowski | Initial version |
| 2023-03-24 | VCS 1.7 | CESDHC-6675 | Piotr Lewandowski | Added the service account information |

# 1. General Information

## 1.1 Purpose

This document describes the requirements, the process and the ATF tool integration for a specific Service Request (SR). It should deliver additional information and very low level design facts to support the development team to understand the required development and integration steps.

## 1.2 Audience

This document is intended for Atos Cloud Services Engineers and Architects responsible for VMware Cloud Services (VCS) solution implementation and maintenance.

## 1.3 Scope

The scope of the standard service request (SSR) documentation covers the following:

- High level description of service request
- Prerequisities and dependencies
- Process workflow of service request
- Detailed service request configuration
- Installation of service request

## 1.4 Related documents

| Document name | Document location       |
| ------- | ---------- |
| VCS HLD     | [HLD VMware Cloud Services](hldDigitalHybridCloud.md) |

## 1.5 Requirement Levels

This document is following the principles below to categories all requirements and design decisions.

| Term | Meaning |
| :---: | --- |
| MUST | The definition is an absolute requirement of the specification. |
| MUST NOT | The definition is an absolute prohibition of the specification |
| SHOULD | There may exist valid reasons in particular circumstances to ignore a particular item, but the full implications must be understood and carefully weighed before choosing a different course |
| SHOULD NOT | There may exist valid reasons in particular circumstances when the particular behaviour is acceptable or even useful, but the full implications should be understood and the case carefully weighed before implementing any behaviour described with this label |
| MAY | Any design decisions that are not classified as MUST and SHOULD or covering optional feature that is not general available for VCS product |

# 2. Architecture Overview

## 2.1 Business and Solution Requirements

The table below provides known requirements mandatory to be incorporated into design decisions of VCS Secret Management described in this LLD.

##### Table 2. Initial Requirements

| ID | Requirement description | Requirement Source | Requirement Level |
| :---: | --- | :---: | :---: |
| R001 | Allow the user to add, modify or remove backup policy for a given VM | HLD | MUST |
| R002 | Allow the user to use a custom retention period when performing backup on demand | HLD | MUST |
| R003 | Dynamically populate input fields with available backup policies and restore points | HLD | MUST |
| R004 | Support VRA Cloud and On-prem | HLD | MUST |
| R005 | Provide CEB Avamar authentication through VCS Password store (HashiVault) | HLD | MUST |

## 2.2 High Level Description

Backup/Restore SSRs in VCS consist of 3 requests:

1. Manage backup policy - depending on the user selection the SSR adds/removes a given VM from a backup policy or assigns a new backup policy
2. Backup on demand - performs an on-demand backup either using the assigned backup policy or a custom retention period defined by the user
3. Restore a VM from the backup - restores a VM from backup using a restore point selected by the user.

These 2nd day standard service requests are implemented as custom resource actions for existing deployments in Service Broker/Cloud Assembly. Each custom resource action has an input form which uses VRO action to dynamically populate required fields and once the request is submitted, it triggers a VRO workflow to execute the SSR. The workflow uses REST API to communicate with Avamar. The only supported backup solution at the time of writing this document is CEB Avamar.

High Level Process Flow is illustrated by below diagram:

![Process Flow](images/lldManageBackupRestoreSsrs/workflowOverview.svg)

The following request types are possible:

- Add VM to a selected backup policy
- Remove VM from a backup policy
- Change the backup policy to a new one

## 2.3 Prerequisities and dependencies

### Assumptions

- CEB is implemented on VCS site with Avamar version 19.x or higher
- During the transaction execution the actual backup policy for server is always gathered (stored) in the CMDB
- Backup membership for servers is mandatory (forced by default backup policy)
- There is no need to create any maintenance window for transaction because no downtime is required for backup request
- Above transaction is not applicable for database (SQL/MySQL/Oracle) servers

### VCS prerequisities

- VCS is after hardening state with vRO/vRA integration in place
- Healthy cloud extensibilty proxy (VRA Cloud only) and vRO
- SSRConfig configuration element and REST Hosts created in VRO
- Healthy VCS proxy (VRA Cloud Only)

### License requirements

No additional licensing is required as long as vRA, vRO and all endpoints are licensed.

### vRO requirements and dependencies

- Imported latest version of the SSR package from github repository
- vRO configuration file created and filled in
- Healthy vRO integration under vRA cloud tenant organization
- vRA rest host configuration
- Avamar rest host configuration
- Enabled vCenter plugin for compute
- Imported related workflows that are consumed by SSR workflow
- For vRA SaaS a VMware service account needs to be created as described in the [Permissions](#permissions) chapter

# 3. Detailed logical design

## 3.1 Manage VM Backup Policy SSR

### Service request overview

The service request has 3 possible operation types:

- Add
- Modify
- Remove

When the Add or Modify operation is selected by the user, Backup Policy field is displayed and populated dynamically with the list of policies defined in Avamar.

The following fields are read-only and populated automatically:

- Machine Name
- Current Backup Policy
- VM vCenter

Add operation:

![Day2 Action form - Manage policy - Add](images/lldManageBackupRestoreSsrs/actionFormManagePolicyAdd.png)

Modify operation:

![Day2 Action form - Manage policy - Modify](images/lldManageBackupRestoreSsrs/actionFormManagePolicyModify.png)

Remove operation:

![Day2 Action form - Manage policy - Remove](images/lldManageBackupRestoreSsrs/actionFormManagePolicyRemove.png)

### Service request inputs

Service Broker form for this 2nd day action has 2 inputs that need to be provided by the user:

| value   | Type       | Description              |Mandatory        |
| ------- | ---------- | ------------------------ | --------------- |
| operation| String | Operation type (Add/Modify/Remove)| YES |
| backupPolicy| String| Backup policy name that will be used to trigger VM backup on. The list of values is pulled from Avamar dynamically.|for Add/Modify operation|

### vRO workflow steps

Main steps for the **dhcManageVmBackupPolicy** workflow are:

- Get vRA refresh token from the password store (Hashivault) and generate bearer token
- Get Avamar credentials from the password store (HashiVault) and obtain the bearer token
- Get the Domain ID from Avamar using vCenter UUID
- Verify if the operation type is Add/Modify or Remove

For Add/Modify:

- Validate backup policy and rule on Avamar
- Verify if the new backup policy is different than the current one
- Update tags on vRA and vCenter
- Get VM object based on unique VM instance ID and vCenter UUID
- Add client to backup
- Sync all clients on Avamar
- Validate if VM was successfully added into requested backup policy

For Remove:

- Change the VM tag in VRA to **noBackup**
- Sync all clients on Avamar

![Workflow schema](images/lldManageBackupRestoreSsrs/dhcManageVmBackupPolicyWorkflow.png)

### Inputs

There are 7 inputs that are required for this workflow. All of them are passed from the Service Broker request, however not all of them are presented to the user on the form.

| value   | Type       | Description              |Mandatory        |
| ------- | ---------- | ------------------------ | --------------- |
| backupPolicy| String| Backup policy name that will be used to trigger VM backup on. The list of values is pulled from Avamar prior to running the workflow|YES|
| virtualMachine| VC:VirtualMachine| Virtual Machine object |YES|
| vmInstanceUuid | String | Unique ID of requested virtual machine from vCenter. Not shown in the input form | YES|
| vCenterUuid | String | Unique ID of vCenter instance of requested virtual machine. Not shown in the input form| YES|
| vcenterName | String | FQDN of VM vCenter server| YES|
| currentBackupPolicy| String | Current VM backup policy | YES |
| operation| String | Operation type (Add/Modify/Remove)| YES |

## 3.2 Backup On-Demand SSR

### Service request overview

The service request has 2 possible backup types:

- By Policy
- Custom Retention

When the **Custom Retention** backup type is selected by the user, additional **Custom Retention** field is displayed and needs to be filled in by the user with a number of days for the retention period.

The following fields are read-only and populated automatically:

- Machine Name
- VM vCenter
- Current Backup Policy

![Day2 Action form - backup](images/lldManageBackupRestoreSsrs/actionFormBackup.png)

### Service request inputs

| value   | Type       | Description              |Mandatory        |
| ------- | ---------- | ------------------------ | --------------- |
| backupType | String| Type of On-Demand Backup - value can either be **By Policy** or **Custom Retention**|YES|
| customRetention | String | if the backupType is custom, this value determines the retention period |Only if the backupType is **Custom Retention**|

### vRO workflow steps

Main steps for the **dhcBackupOnDemand** workflow are:

- Get Avamar credentials from the password store (HashiVault)
- Get Avamar bearer token
- Prepare backup details (get vcenter id, client id, etc.)
- Validate if client is present in Avamar - if not, Add client to backup
- Validate if client OS is supported - if not, fail the workflow
- Verify if the backup is to be done by Policy or with custom retention
- Execute backup on-demand for requested VM with proper settings
- Get backup job id and monitor once finished

![Workflow schema](images/lldManageBackupRestoreSsrs/dhcBackupOnDemandWorkflow.png)

#### Inputs

There are 7 inputs that are required for this workflow. All of them are passed from the Service Broker request, however not all of them are presented to the user on the form.

| value   | Type       | Description              |Mandatory        |
| ------- | ---------- | ------------------------ | --------------- |
| backupPolicy| String| Backup policy name that will be used to trigger VM backup on. The list of values is pulled from Avamar prior to running the workflow|YES|
| virtualMachine| VC:VirtualMachine| Virtual Machine object |YES|
| vmInstanceUuid | String | Unique ID of requested virtual machine from vCenter. Not shown in the input form| YES|
| vCenterUuid | String | Unique ID of vCenter instance of requested virtual machine. Not shown in the input form| YES|
| vcenterName | String | FQDN of VM vCenter server| YES|
| backupType | String| Type of On-Demand Backup - value can either be **byPolicy** or **customRetention**|YES|
| customRetention | String | if the backupType is custom, this value determines the retention period |NO|

## 3.3 Backup Restore SSR

### Service request overview

The service request restores a VM from a selected restore point. The **Restore backup point** field is dynamically populated with a list of restore points obtained from Avamar via a VRO action.

**Note:** Restore SSR supports only option to restore to original (existing) VM (no restore to new VM is supported now)

The following fields are read-only and populated automatically:

- Machine Name
- VM vCenter

![Day2 Action form - restore](images/lldManageBackupRestoreSsrs/actionFormRestore.png)

### Service request inputs

| value   | Type       | Description              |Mandatory        |
| ------- | ---------- | ------------------------ | --------------- |
| Backup Restore Point | String| Restore point selected by the user |YES|

### vRO workflow

#### Steps

Main steps for the **dhcBackupRestore** workflow are:

- Get Avamar credentials from password store (HashiVault)
- Get Avamar bearer token
- Get request metadata
- Verify if the client is found in Avamar
- Fetch the Guest OS information
- Verify that the Guest OS is supported - if not, fail the workflow
- Get all VM disks
- Verify if the VM is powered off - if not, force the shutdown
- Execute Backup Restore
- Verify the Restore job status
- Verify if the VM is powered on, - if not, power it on
- Wait until the VM Tools are started before ending the workflow

![Workflow schema](images/lldManageBackupRestoreSsrs/dhcBackupRestoreWorkflow.png)

#### Inputs

There are 5 inputs that are required for this workflow. All of them are passed from the Service Broker request, however not all of them are presented to the user on the form.

| value   | Type       | Description              |Mandatory        |
| ------- | ---------- | ------------------------ | --------------- |
| backupPoint| String| Avamar Restore backup point|YES|
| virtualMachine| VC:VirtualMachine| Virtual Machine object |YES|
| vmInstanceUuid | String | Unique ID of requested virtual machine from vCenter. Not shown in the input form| YES|
| vCenterUuid | String | Unique ID of vCenter instance of requested virtual machine. Not shown in the input form| YES|
| vcenterName | String | FQDN of VM vCenter server| YES|

## 3.4 VRO Resources

The VRO resources in the following sections are used across all 3 Standard Service Requests.

### Actions

Following table describes vRO actions implemented in this standard service request:

| Name  |  Package name | Description and Purpose            |     Workflow      |
| ----------- | ------------- | ------------------------ | ------------------------|
| getVraVmBackupTag | net.atos.dhc.automation | Obtains the Backup Policy Tag assigned to a given VM. Used by the SSR input form| dhcManageVmBackupPolicy, dhcBackupOnDemand |
| getVmInstanceUuid | net.atos.dhc.automation | Obtains the VM instance UUID of a given VM. Used by the SSR input form| dhcManageVmBackupPolicy, dhcBackupOnDemand, dhcBackupRestore |
| getVcenterNameFromUuid | net.atos.dhc.automation | Obtains the vCenter Name from vCenter plugin using vCenter UUID from vRA. Used by the SSR input form| dhcManageVmBackupPolicy, dhcBackupOnDemand, dhcBackupRestore |
| getAvamarBackupPolicies | net.atos.dhc.automation | Obtains the list of avaialable backup policies from Avamar. The same action is used both by the day2 Manage VM Backup Policy SSR and Day1 Deploy virtual machine SSR. Used by the SSR input form| dhcManageVmBackupPolicy |
| getAvamarBackupPoints | net.atos.dhc.automation | Obtains the list of avaialble backup restore points from Avamar. Used by the Restore SSR input form| dhcBackupRestore |
| getVaultToken| net.atos.dhc.automation | Obtains the Hashivault token using a service account credentials stored in the Configuration file (SSRConfig)| dhcGetAvamarToken, dhcGetVraToken |
| getVaultSecretData| net.atos.dhc.automation | Obtains the secret stored in Hashivault | dhcGetAvamarToken, dhcGetVraToken |
| getBearerTokenVra | net.atos.dhc.automation | Obtains API bearer token from vRA cloud | dhcGetVraToken |
| getAvamarBearer | net.atos.dhc.automation | Obtains API bearer token from Avamar | dhcGetAvamarToken |
| shutdownVMandForce| com.vmware.library.vc.vm.power | Powers off the VM during backup restore, it is a default action available within the package | dhcBackupRestore |
| vim3WaitToolsStarted| com.vmware.library.vc.vm.tools | Waits for vmware tools to be running for the VM that was powered on after restore - it is a default action available within the package | dhcBackupRestore |

### Workflows

All workflows are stored in the VRO-Workflows GIT repository. The Following table describes each workflow which is used by the Backup Restore standard service request:

| Item name  |  Path | Description and Purpose           |
| ----------- | ------------- | ------------------------ |
| dhcManageVmBackupPolicy | /DHC/2Day Actions/ | Main workflow to start the Manage VM Backup Policy service request |
| dhcBackupOnDemand | /DHC/2Day Actions/ | Main workflow to start the Backup On-Demand service request |
| dhcBackupRestore | /DHC/2Day Actions/ | Main workflow to start the Backup Restore service request |
| dhcGetAvamarToken  | /DHC/2Day Actions | Workflow to gather Avamar credentials from Vault and generate a bearer token |
| dhcGetVraToken   | /DHC/2Day Actions | Workflow to gather vRA refresh token from Vault and generate a bearer token |

### Configurations

Following table describes configuration items used by the service request:

| Name  |  Path | Description and Purpose            |
| ----------- | ------------- | ------------------------ |
| proxyHost| /DHC/SSRConfig | VCS Proxy server IP address or hostname |
| proxyPort| /DHC/SSRConfig | VCS Proxy server port |
| vaultUsername | /DHC/SSRConfig | Service account username used for accessing Hashi vault |
| vaultPassword | /DHC/SSRConfig | Service account password used for accessing Hashi Vault|
| vaultServer | /DHC/SSRConfig | FQDN of the Hashivault server |
| vaultPort | /DHC/SSRConfig | Hashivault server port  |
| vaultVraTokenPath | /DHC/SSRConfig | Hashivault path to the VRA refresh token  |
| vaultAvamarBackupPath | /DHC/SSRConfig | Hashivault path to Avamar username & password  |
| vraRestHost | /DHC/SSRConfig | vRA rest host configuration  |
| avamarRestHost | /DHC/SSRConfig | Avamar rest host configuration  |
| backupType | /DHC/SSRConfig | Backup Solution - by default it's Avamar  |
| vraType | /DHC/SSRConfig | VRA Type: saas or on-prem  |

### Permissions

Following permission are required to configure and enable service request in vRA

- vRA cloud authorization token
- Cloud Assembly Administrator role
- Service Broker Administrator role
- Service Broker day2 policy is defined to enable service request as custom day2 resource action under vRA cloud assembly

For the SSRs to function a service account is required in order to generate a dedicated API refresh token. The token will be used to handle VRA API calls within the vRO workflows. Depending on the vRA type it's either an internal domain account or a VMware account.

**The gollowing table lists required service accounts**

| Account name                  | Description                                  |
| ----------------------------- | ------------------------------------------ |
| `svc-{locationCode}-{tenant}-vro01@atos.net`     | VMware service account to handle vRA API requests from ABX/VRO for vRA SaaS. To order the account please follow the document: [VCS build guide chapter for Vmware service account](../workInstructions/dhcBuildGuide.md#vmware-service-account)  |
| svc-{locationCode}-vro01@{vcs domain name} | Internal VCS Management Active Directory domain service account to handle vRA API requests from VRO to On-Prem vRA. |

# 4. Installation

Installation of standard service request is performed during execution of the below work instruction as part of the VCS build. The procedure is automated with Ansible playbooks, which:

- configure REST Hosts in VRO
- create a Configuration file (SSRCOnfig)
- create day2 custom resource actions in vRA
- update Service Broker form and day2 policy
- create VRO service account, assign permissions in vRA and store its refresh token in Hashivault

Installaton of service request should take no more then 4 hours.

| Document name | Document location       |
| ------- | ---------- |
| vRA Cloud: wiTenantBuilder | [GLB-CES-PrivateCloud/DHC-Documentation](../workInstructions/wiTenantBuilder.md) |
| vRA On-Prem: wiTenantBuilderVraOnPrem | [GLB-CES-PrivateCloud/DHC-Documentation](../workInstructions/wiTenantBuilderVraOnPrem.md) |
