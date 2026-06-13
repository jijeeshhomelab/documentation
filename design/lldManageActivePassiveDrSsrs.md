# Manage VM Active-Passive DR SSRs Documentation

# Table of Contents

- [Manage VM Active-Passive DR SSRs Documentation](#manage-vm-active-passive-dr-ssrs-documentation)
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
  - [2.2 High Level Description](#22-high-level-description)
  - [2.3 Prerequisities and dependencies](#23-prerequisities-and-dependencies)
    - [VCS prerequisities](#vcs-prerequisities)
    - [License requirements](#license-requirements)
    - [vRO requirements and dependencies](#vro-requirements-and-dependencies)
- [3. Detailed logical design](#3-detailed-logical-design)
  - [3.1 Manage VM DR Active-Passive Protection SSR](#31-manage-vm-dr-active-passive-protection-ssr)
    - [Service request overview](#service-request-overview)
    - [Service request inputs](#service-request-inputs)
    - [vRO workflow steps](#vro-workflow-steps)
    - [vRO workflow inputs](#vro-workflow-inputs)
  - [3.4 VRO Resources](#34-vro-resources)
    - [Actions](#actions)
    - [Workflows](#workflows)
    - [Configurations](#configurations)
    - [Permissions](#permissions)
- [4. Installation](#4-installation)

# Changelog

| Date       | TOS     | Issue     | Author            | Description                     |
|------------|---------|-----------|-------------------|---------------------------------|
| 2023-05-26 | VCS 1.8 | VCS-9663  | Piotr Lewandowski | Initial version                 |
| 2023-11-30 | VCS 1.8 | VCS-11267 | Marcin Kujawski   | Updates based on feature review |

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

| Document name | Document location                                    |
|---------------|------------------------------------------------------|
| VCS HLD       | [HLD VMware Cloud Services](hldDigitalHybridCloud.md) |

## 1.5 Requirement Levels

This document is following the principles below to categorize all requirements and design decisions.

|    Term    | Meaning                                                                                                                                                                                                                                                         |
|:----------:|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    MUST    | The definition is an absolute requirement of the specification.                                                                                                                                                                                                 |
|  MUST NOT  | The definition is an absolute prohibition of the specification                                                                                                                                                                                                  |
|   SHOULD   | There may exist valid reasons in particular circumstances to ignore a particular item, but the full implications must be understood and carefully weighed before choosing a different course                                                                    |
| SHOULD NOT | There may exist valid reasons in particular circumstances when the particular behaviour is acceptable or even useful, but the full implications should be understood and the case carefully weighed before implementing any behaviour described with this label |
|    MAY     | Any design decisions that are not classified as MUST and SHOULD or covering optional feature that is not general available for VCS product                                                                                                                      |

# 2. Architecture Overview

## 2.1 Business and Solution Requirements

The table below provides known requirements mandatory to be incorporated into design decisions of VCS Secret Management described in this LLD.

|  ID  | Requirement description                                            | Requirement Source | Requirement Level |
|:----:|--------------------------------------------------------------------|:------------------:|:-----------------:|
| R001 | Allow the user to add or remove DR protection for a given VM       |        HLD         |       MUST        |
| R002 | Allow the user to select the RPO and Priority                      |        HLD         |      SHOULD       |
| R003 | Dynamically populate input fields with available Protection Groups |        HLD         |       MUST        |
| R004 | Dynamically populate input fields with available Recovery Plans    |        HLD         |      SHOULD       |
| R005 | Support VRA Cloud and On-prem                                      |        HLD         |       MUST        |

## 2.2 High Level Description

Manage DR Protection SSR in VCS is a single request that contains 2 action types:

1. Protect - when this action type is selected, the user is presented with a choice of Recovery Plan, Protection Group, RPO and Priority and the DR protection is enabled with parameters selected by the user. The Recovery Plan and Protection Group fields are populated dynamically using a VRO action.
2. Remove - when this option is selected the user can submit a request to remove DR protection from a given VM.

These 2nd day standard service requests are implemented as custom resource actions for existing deployments in Service Broker/Cloud Assembly. Each custom resource action has an input form which uses VRO action to dynamically populate required fields and once the request is submitted, it triggers a VRO workflow to execute the SSR. The workflow uses REST API to communicate back with vRA as well as vRO plugins to communicate with SRM and vSphere Replication. The only supported DR solution at the time of writing this document is Active/Passive.

High Level Process Flow is illustrated by below diagram:

![Process Flow](images/lldManageActivePassiveDrSsrs/workflowOverview.svg)

## 2.3 Prerequisities and dependencies

### VCS prerequisities

- VCS is after hardening state with vRO/vRA integration in place
- Healthy cloud extensibilty proxy (VRA Cloud only) and vRO
- Healthy VCS proxy (VRA Cloud Only)
- Fully configured and healthy SRM and vSphere Replication

### License requirements

No additional licensing is required as long as vRA, vRO and all endpoints are licensed.

### vRO requirements and dependencies

- Imported latest version of the SSR package from github repository
- vRO configuration file created and filled in (SSRConfig)
- vRO DR configuration file filled in with required values (drConfigurationFile)
- Healthy vRO integration under vRA cloud tenant organization
- vRA rest host configuration
- Enabled vCenter plugin for compute
- Enabled SRM plugin
- Enabled vSphere Replication Plugin
- Imported related workflows that are consumed by SSR workflow
- For vRA SaaS a VMware service account needs to be created as described in the [Permissions](#permissions) chapter

# 3. Detailed logical design

## 3.1 Manage VM DR Active-Passive Protection SSR

### Service request overview

The service request has 2 possible operation types:

- Protect
- Remove

When the Protect operation is selected by the user, Recovery Plan and Protection Group field is displayed and populated dynamically. Protection Groups are filtered based on the Recovery Plan selection.

Protect operation:

![Day2 Action form - Manage DR - Protect](images/lldManageActivePassiveDrSsrs/protectAction.png)

Remove operation:

![Day2 Action form - Manage DR - Remove](images/lldManageActivePassiveDrSsrs/removeAction.png)

### Service request inputs

Service Broker form for this 2nd day action has 5 inputs that need to be provided by the user:

| value             | Type   | Description                                                                                                          | Mandatory                   |
|-------------------|--------|----------------------------------------------------------------------------------------------------------------------|-----------------------------|
| actionType        | String | Operation type (Protect/Remove)                                                                                      | YES                         |
| drRecoveryPlan    | String | List of recovery plans defined in SRM for a given protected site. The list of values is pulled from SRM dynamically. | for Protect operation - Yes |
| drProtectionGroup | String | List of Protection Groups assigned to a selected Recovery Plan. The list of values is pulled from SRM dynamically.   | for Protect operation - Yes |
| drPriority        | String | Startup Priority for the VM in a Recovery Plan. Presented as a drop-down list                                        | for Protect operation - Yes |
| drRpo             | String | Recovery Point Objective value for vSphere Replication. Presented as a drop-down list                                | for Protect operation - Yes |

### vRO workflow steps

Main steps for the **dhcProtectVmDrActivePassive** workflow are:

- Get vRA refresh token from the password store (Hashivault) and generate bearer token
- Verify if the operation type is Protect or Remove.

For Protect:

- Gather inputs
- Extract VM details from vRA
- Validate VM UUID in vCenter
- Disconnect CD-ROM from the VM
- Get SRM service account credentials from Hashivault
- Register login credentials for a vCenter Server site that is paired with a local site
- Set inputs for the built-in vSphere Replication Workflow (Protect Multiple VMs) - such as the datastore name, disk type, virtual machine object
- Run the out-of-the-box workflow to configure vSphere replication for the VM
- Run the out-of-the-box SRM Workflow to add the VM to a given Protection Group
- Run the out-of-the-box SRM Workflow to configure the VM settings in the Recovery Plan (Priority Group)
- Update the VM tags in vRA

For Remove:

- Trigger **dhcRemoveVmDrActivePassive** workflow
- Update VM tags in vRA

![Workflow schema - Protect](images/lldManageActivePassiveDrSsrs/dhcProtectVmDrActivePassiveWorkflow.png)

Main steps for the **dhcRemoveVmDrActivePassive** workflow are:

- Get metadata and get VM object
- Get Protection Groups
- Get Protection Group the VM is assigned to
- Remove the VM from vSphere Replication and the Protection Group
- Verify if the VM has been removed from vSphere Replication and the Protection Group. If not, try again (up to 3 times)

![Workflow schema - Remove](images/lldManageActivePassiveDrSsrs/dhcRemoveVmDrActivePassiveWorkflow.png)

This workflow doesn't have any inputs.

### vRO workflow inputs

There are 4 inputs that are required for this workflow. All of them are passed from the Service Broker request.

| value             | Type   | Description                                   | Mandatory                   |
|-------------------|--------|-----------------------------------------------|-----------------------------|
| drRpo             | String | RPO selected in the input form                | YES - for Protect operation |
| drProtectionGroup | String | Protection Group selected in the input form   | YES - for Protect operation |
| drPriority        | String | Priority Group for the Recovery Plan settings | YES - for Protect operation |
| actionType        | String | Protect/Remove                                | YES                         |

## 3.4 VRO Resources

The VRO resources in the following sections are used across all Workflows.

### Actions

Following table describes vRO actions implemented in this standard service request:

| Name                        | Package name                 | Description and Purpose                                                      | Workflow                                 |
|-----------------------------|------------------------------|------------------------------------------------------------------------------|------------------------------------------|
| getRemoteVcSiteDatastores   | com.vmware.library.vr        | Get the available target datastores on a remote vCenter Server site pairing. | dhcProtectVmDrActivePassive              |
| getSupportedDiskFormats     | com.vmware.library.vr        | Get supported disk types from vSphere Replication                            | dhcProtectVmDrActivePassive              |
| getStorageProfileIds        | com.vmware.library.vr        | Get the list of VSAN storage policies from the remote site                   | dhcProtectVmDrActivePassive              |
| getAllVMsMatchingRegexp     | com.vmware.library.vc.vm     | Get the list of VMs matching a defined regular expression                    | dhcProtectVmDrActivePassive              |
| listUnassignedReplicatedVms | com.vmware.library.srm       | Obtains the list of all replicated VMs without assigned Protection Group     | dhcProtectVmDrActivePassive              |
| getRecoveryPlans            | com.vmware.library.srm.plan  | Obtains the list of Recovery Plans from SRM  (built-in action)               | dhcProtectVmDrActivePassive              |
| dhcGetVmObject              | net.atos.dhc.automation      | Obtains the VM object from vCenter based on the UUID                         | dhcRemoveVmDrActivePassive               |
| getProtectionGroups         | com.vmware.library.srm.group | Obtains the list of Protection Groups                                        | dhcRemoveVmDrActivePassive               |
| getSRMProtectionGroup       | net.atos.dhc.automation      | Obtains the list of Protection Groups based on the selected Recovery Plan    | dhcProtectVmDrActivePassive - input form |
| getSRMRecoveryPlan          | net.atos.dhc.automation      | Obtains the list of Recovery Plans for a site matching the location code     | dhcProtectVmDrActivePassive - input form |

### Workflows

All workflows are stored in the VRO-Workflows GIT repository. The Following table describes each workflow which is used by the Manage VM DR Active/Passive Protection standard service request:

| Item name                   | Path                       | Description and Purpose                                                |
|-----------------------------|----------------------------|------------------------------------------------------------------------|
| dhcProtectVmDrActivePassive | /dhcDisasterRecovery/Day2/ | Main workflow to start the Manage VM DR A/P Protection service request |
| dhcRemoveVmDrActivePassive  | /dhcDisasterRecovery/Day2/ | Sub-workflow triggered when the action type is set to Remove           |

### Configurations

Following table describes configuration items used by the service request:

| Name                     | Path                            | Description and Purpose                                                                                                                                          |
|--------------------------|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| remoteLsUrl              | /Library/DR/drConfigurationFile | Path to PSC lookup service - e.g, gre22vcs002.nx2dhc.next/lookupservice/sdk                                                                                      |
| remoteSite               | /Library/DR/drConfigurationFile | Remote vSphere Replication Site                                                                                                                                  |
| srmSite                  | /Library/DR/drConfigurationFile | Local SRM Site                                                                                                                                                   |
| remoteSiteCode           | /Library/DR/drConfigurationFile | Remote site location code                                                                                                                                        |
| remoteDomain             | /Library/DR/drConfigurationFile | Remote Domain name, e.g. nx2dhc.next                                                                                                                             |
| vaultPort                | /Library/DR/drConfigurationFile | Port used on vault server in Local and Remote sites (default 8200)                                                                                               |
| vaultServer              | /Library/DR/drConfigurationFile | IP of vault server in Local site                                                                                                                                 |
| vaultUser                | /Library/DR/drConfigurationFile | Username to logon to vault e.g. svc-<locationCode>-ans03@<customerCode>dhc.next                                                                                  |
| vaultPassword            | /Library/DR/drConfigurationFile | Password to logon to vault in Local site                                                                                                                         |
| vaultSecretPathRemoteSrm | /Library/DR/drConfigurationFile | Path to REMOTE SRM credentials which are stored on local vault e.g. secret/data/<customerCode>/<locationCode>/activedirectory/svc-<RemoteSiteLocationCode>-srm01 |
| vraRestHost              | /DHC/SSRConfig                  | vRA rest host configuration                                                                                                                                      |
| site                     | /Library/DR/drConfigurationFile | Local vSphere Replicaton Site                                                                                                                                    |

### Permissions

Following permission are required to configure and enable service request in vRA

- vRA cloud authorization token
- Cloud Assembly Administrator role
- Service Broker Administrator role
- Service Broker day2 policy is defined to enable service request as custom day2 resource action under vRA cloud assembly

For the SSRs to function a service account is required in order to generate a dedicated API refresh token. The token will be used to handle VRA API calls within the vRO workflows. Depending on the vRA type it's either an internal domain account or a VMware account.

**The following table lists required service accounts**

| Account name                                 | Description                                                                                                                                                                                                                                     |
|----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `svc-{locationCode}-{tenant}-vro01@atos.net` | VMware service account to handle vRA API requests from ABX/VRO for vRA SaaS. To order the account please follow the document: [VCS build guide chapter for Vmware service account](../workInstructions/dhcBuildGuide.md#vmware-service-account) |
| svc-{locationCode}-vro01@{vcs domain name}   | Internal VCS Management Active Directory domain service account to handle vRA API requests from VRO to On-Prem vRA                                                                                                                              |

Permissions required in vRA for the above Service Accounts:

- Organization member

- Cloud Assembly Administrator
- Service Broker Administrator
- Project Member

# 4. Installation

Installation of standard service request is performed during execution of the below work instruction as part of the VCS build. The procedure is automated with Ansible playbooks, which:

- configure REST Hosts in VRO
- create a Configuration file (SSRCOnfig)
- create day2 custom resource actions in vRA
- update Service Broker form and day2 policy
- create VRO service account, assign permissions in vRA and store its refresh token in Hashivault

Installation of service request should take no more then 4 hours.

| Document name                         | Document location                                                                         |
|---------------------------------------|-------------------------------------------------------------------------------------------|
| vRA Cloud: wiTenantBuilder            | [GLB-CES-PrivateCloud/DHC-Documentation](../workInstructions/wiTenantBuilder.md)          |
| vRA On-Prem: wiTenantBuilderVraOnPrem | [GLB-CES-PrivateCloud/DHC-Documentation](../workInstructions/wiTenantBuilderVraOnPrem.md) |
