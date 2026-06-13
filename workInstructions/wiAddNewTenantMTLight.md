# Enable Sub-tenant in Multitenancy Light

- [Enable Sub-tenant in Multitenancy Light](#enable-sub-tenant-in-multitenancy-light)
- [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
- [1.1 Prerequisites](#11-prerequisites)
- [2 Automated Steps](#2-automated-steps)
  - [2.1 Workflow execution](#21-workflow-execution)
  - [2.2 Workflow execution result](#22-workflow-execution-result)
- [3 Manual Steps](#3-manual-steps)
  - [3.1 Check Cloud Template for a given VRA Project](#31-check-cloud-template-for-a-given-vra-project)
  - [3.2 Test the template](#32-test-the-template)
  - [3.3 Release the new version](#33-release-the-new-version)
  - [3.4 Network Profiles](#34-network-profiles)
  - [3.5 SDN Infrastructure](#35-sdn-infrastructure)
  - [3.6 SDN Security](#36-sdn-security)
  - [3.7 SDN network configuration](#37-sdn-network-configuration)

# Changelog

| Date | Issue | Author   | TOS  | Description  |
| ------- | ---------- | -------- | --------------- | ------------ |
| 08.12.2022 | amc-4474 | Piotr Lewandowski | | Initial version |
| 12.12.2022 | amc-4547 | Michal Pindych  | | SDN section update |
| 04.04.2023 | VCS-5966 | Adrian Ilea  | | Siemens customisation removal, Automated Steps section update  |
| 09.04.2024 | VCS-12601| Marcin Kujawski | | Update work instruction with recent changes |

## Introduction

### Purpose

Enable a new sub-tenant for VCS in a Multitenancy Light approach.

### Audience

- VCS Operations
- VCS Engineers

### Scope

- Update a CloudAssembly template
- Update a Service Broker Catalog Item
- Adjust SDN Configuration

# 1.1 Prerequisites

1. The new tenant Resource Pool needs to be added to ServiceNow CMDB
2. The tenant name value needs to be provided
3. **Project scope** for existing Parent Project has to be set to *Any project* (check assignVmName, cusVmNameValidation, activePassivedrSubscription in Cloud Assembly/Extensibility/Subscriptions)
![000 Prereq](./images/wiAddNewTenantMTLight/prereq01_1.png)
![001 Prereq](./images/wiAddNewTenantMTLight/prereq01.png)
4. *Share with all projects in this organization* option of assignVmNameAction needs to be checked (in Settings in Cloud Assembly/Extensibility/Actions/)
![002 Prereq](./images/wiAddNewTenantMTLight/prereq02.png)

# 2 Automated Steps

Steps below have been automated using a VRO workflow. However, after successfully running the workflow, please refer to the manual steps in chapter 3 to verify that the results are as expected.

The workflow performs the following steps:

1. Create vCenter Resource Pool.
2. Create new project for tenant.
3. Clone blueprint "Deploy virtual machine" from parent project.
4. Release blueprint.
5. Create content source.
6. Create sharing policy.
7. Add Resource Pool the Cloud Zone.
8. Assign tags to the new Resource Pool.

## 2.1 Workflow execution

- Log in to the VRO in a site where the new sub-tenant is to be created and navigate to Workflows -> OneIaaS -> Multitenancy-Light folder and run the **EnableTenant** workflow:

  ![000 Automat](./images/wiAddNewTenantMTLight/automation_workflow_0.png)

and then fill in the input form.

- In the **New tenant name** field type in the sub-tenant name matching the value defined in ServiceNow, in this case *subTenant01*
- In the **Compute cluster** field select the appropriate vSphere cluster

  ![001 Automat](./images/wiAddNewTenantMTLight/automation_workflow_1.png)

- In the **Parent compute resource pool** field nagivate to the cluster where the new resource pool should be created and select the **Resources** checkbox under that cluster

  ![002 Automat](./images/wiAddNewTenantMTLight/automation_workflow_2.png)

- Depending on whether the cluster is DR-enabled or not, select the appropriate value from **DR enabled on cluster** drop-down list
- In the **Cloud Zone** field type the name of the zone configured for the corresponding vCenter/cluster, in this case *hrk0101*:

  ![003 Automation](./images/wiAddNewTenantMTLight/automation_workflow_3.png)

- In the **Parent Project of the blueprints** field type in the project name corresponding to the site that is being updated with the new tenant, in this case  *prd001*

  ![004 Automation](./images/wiAddNewTenantMTLight/automation_workflow_4.png)

- Once all fields are populated click **Run**

  ![005 Automation](./images/wiAddNewTenantMTLight/automation_workflow_5.png)

- Example workflow run

  ![006 Automation](./images/wiAddNewTenantMTLight/automation_wf_6.png)

**Note:** Repeat these steps for all clusters and all locations where the new tenant is to be enabled.

## 2.2 Workflow execution result

Assembler Validation:

- The new VRA Project will be created with blueprint cloned from the parent project.

  ![007 Automation](./images/wiAddNewTenantMTLight/automation_res_1.png)

- The newly created vCenter Resource Pool will be added in the VRA Cloud Zone as a result of the workflow execution. The *tenant* tag should match the value defined in ServiceNow. Example: *subTenant01*.

  ![007 Automation](./images/wiAddNewTenantMTLight/automation_res_3.png)

- New Blueprint will be cloned and assigned to subtenant project.
  >Note: Only one blueprint should be created/cloned - "Deploy virtual machine"

  ![007 Automation](./images/wiAddNewTenantMTLight/automation_res_4.png)
  
Service Broker Validation:

- New Content Source will be created.
  
  ![007 Automation](./images/wiAddNewTenantMTLight/automation_res_5.png)

- New Content will be created with new blueprint.

  ![007 Automation](./images/wiAddNewTenantMTLight/automation_res_6.png)

- New Content Sharing Policy will be created for subtenant.

  ![007 Automation](./images/wiAddNewTenantMTLight/automation_res_7.png)

- New Catalog Item will be created that corresponds to new blueprint.

  ![007 Automation](./images/wiAddNewTenantMTLight/automation_res_8.png)

# 3 Manual Steps

**Note:** Steps 3.1-3.3 from the following procedure need to be followed only if Customer is using custom blueprints.

## 3.1 Check Cloud Template for a given VRA Project

In order to enable Multitenancy Light, make sure that the Cloud Template for a given project contains the necessary logic, which places the VM in a resource pool based on the tenant tag, as well as the location and DR type (if applicable). Navigate to the Design Section and open the Blueprint Template for a given site/project.

The logic in the template should look like this:

![001 Cloud Template](./images/wiAddNewTenantMTLight/CloudTemplate1.png)

## 3.2 Test the template

- Once the Cloud Template has been updated it needs to be tested to verify that the syntax is correct and the VM placement works as expected. Click the **Test** button. Fill in the form with the appropriate values for the new tenant (in this case *finalTest3*), then click **Test**

  ![002 Cloud Template](./images/wiAddNewTenantMTLight/CloudTemplate2.png)

- If the test was completed successfully, click on the Provisioning Diagram to display the details of the test request

  ![003 Cloud Template](./images/wiAddNewTenantMTLight/CloudTemplate3.png)

- Navigate to the Machine Allocation section and select the Virtual Machine component

- Make sure that the placement was determined properly and the correct Resource Pool has been chosen based on the tags

  ![004 Cloud Template](./images/wiAddNewTenantMTLight/CloudTemplate4.png)

## 3.3 Release the new version

- Release new version of the updated template by going to Version -> Release -> Create
- Fill in the form with the new version and the description of the changes. Make sure to check the **Release this version to the catalog** checkbox and then click **Create**

   ![005 Cloud Template](./images/wiAddNewTenantMTLight/CloudTemplate5.png)

## 3.4 Network Profiles

Each tenant has pre-defined networks that need to be added into vRA configuration. In order to do this, follow the instructions:

- Tag Compute networks, navigate to Resources -> Networks and click on network.

  ![Network Configuration](./images/wiAddNewTenantMTLight/network_config_1.png)

- Go to bottom into Tags section and add tag in format: `cloudnetwork:<name>`.

  ![Network Configuration](./images/wiAddNewTenantMTLight/network_config_2.png)

- Go to Configure -> Network Profiles and create new network profile. Type a name and set capability tag in format: `cloudprofile:<name>`.

  ![Network Configuration](./images/wiAddNewTenantMTLight/network_config_3.png)

- Go to Networks tab and add all NSX-T networks for this network profile (with tags).

  ![Network Configuration](./images/wiAddNewTenantMTLight/network_config_4.png)

- Once added, select network and click Manage IP Ranges.

  ![Network Configuration](./images/wiAddNewTenantMTLight/network_config_5.png)

- Click New IP Range.

  ![Network Configuration](./images/wiAddNewTenantMTLight/network_config_6.png)

- Select Source as `External` and choose `ipam` provider. Select proper tenant Address space and finally pick the proper subnet. Select it and click Add. Confirm by click double OK.

  ![Network Configuration](./images/wiAddNewTenantMTLight/network_config_7.png)

**Repeat last 3 steps for each network in network profile.**

## 3.5 SDN Infrastructure

Most of the assumptions of multitenancy are consistent with the information contained in the LLD SDN, please refer to the documenation below: <br />

[lldSoftwareDefinedNetworks](../design/lldSoftwareDefinedNetworks.md)

The additional infrastructure component which must be configured for tenant purposes is VRF on T0 router, this task include also BGP configuration.

- Creating VRFs in NSX Manager is done under Networking > Connectivity > Tier-0 Gateways > Add Gateway > VRF:
![001 vrf](./images/wiAddNewTenantMTLight/vrf-1.png)

- When creating a VRF we initially only need to specify a name and a parent Tier-0 Gateway (all other values will be automatically propagated)
![002 vrf](./images/wiAddNewTenantMTLight/vrf-2.png)

- After commit we will be asked to continue configuration, please choose the "yes" option
![003 vrf](./images/wiAddNewTenantMTLight/vrf-3.png)

- Name: Please click "Set" button under INTERFACES and fill up all required information for uplink
![0041 vrf](./images/wiAddNewTenantMTLight/vrf-4.1.png)
![0042 vrf](./images/wiAddNewTenantMTLight/vrf-4.2.png)

- In the end, we must link Tier 1 gateway with the newly created VRF as shown in the picture - please choose the correct Tier-1 and fill up "Linked Tier-0 Gateway" field with the name of the previously created VRF in step 2
![005 vrf](./images/wiAddNewTenantMTLight/vrf-5.png)

- At the end please update BGP configuration with all information agreed with the client

## 3.6 SDN Security

Traffic separation for tenants will be also implemented on NSX-T distributed firewall level. In a dedicated section - the default security group for tenant will be configured. Virtual machines' assignment to a group is based on tags.  Every VM on a platform will have at least 2 security tag

- one tag related to the tenant name
- one tag related to the default security group

Security mechanism implementation for additional tenants

- On NSX-T got to Inventory - > Groups ->  “ADD Group”

  ![001 secu](./images/wiAddNewTenantMTLight/sec-1.png)

- Specify the name for the Security group which includes tenant name and click “Set Members”

  ![002 secu](./images/wiAddNewTenantMTLight/sec-2.png)

- We need to update Membership Criteria to add 2 tags: nsx-security:DefaultSecurityGroup , tenant:{{tenantNameFromSnow}} as the example below.

  ![003 secu](./images/wiAddNewTenantMTLight/sec-3.png)

- After the default security group creation for the tenant we should look at the "effective members" tab to confirm the VM assignment.

  ![004 secu](./images/wiAddNewTenantMTLight/sec-4.png)

- When the security group has been created - we need to update DFW configuration - please navigate to Security - > Distributed Firewall -> "ADD POLICY" - use tenant in the name

  ![001 dsecu](./images/wiAddNewTenantMTLight/d-sec1.png)

- Click the icon located on the left of the section and from drop down menu choose "Add Rule"

  ![002 dsecu](./images/wiAddNewTenantMTLight/d-sec2.png)

- Please create two dedicated rules (for inbound and outbound traffic) using default security groups created before as in example below (by default we are allowing all traffic for a given tenant).

  ![003 dsecu](./images/wiAddNewTenantMTLight/d-sec3.png)

## 3.7 SDN network configuration

Every tenant has a pre-defined networks and IP pools as described below:

- **default network** - unless otherwise specified in this network new VM will be created
- **on-demand NIC** - predefined IP pool from which there is a possibility to create a smaller network if needed, this network can be used only by the users which create them
- **on-demand RFC** - predefined IP pool from which there is a possibility to create a smaller network if needed, support only private IP addresses

<details>
  <summary> Please follow these steps to create default network </summary>

```bash
- creation of NSX segment on active site (including uuid and dr tag) and connect it to specific tenant T1 router 
- creation of NSX segment on passive site (including uuid and dr tag) and connect it to specific tenant T1 router
- contact SNOW team to update CMDB database ( DHC-DevSecOps@atos.net ) and provide to them network name, CIDR and uuid tag 
- update Infoblox configuration with specific network (including default gateway and DNS domain and servers )
- creation on vRA network profiles (only for active site )   
  ```

  Note:  in order to generate uuid we must usthee following python code onthe  online interpreter
  **import uuid; print(uuid.uuid4())**  

  The naming convention for network segment: <br />
  **DE-FTHWDC-139.24.176.16-Siemens-NIC**

- DE – country code
- FTHWDC –  location code
- 139.24.176.16 – network
- Siemens – constant value
- NIC – possible two option: NIC or RFC  

  All details steps are included in the following documentation <br />
  <https://github.com/MAN-SFA/vRA-Documentation/blob/develop/wiAddEditRemoveNsxTNetwork.md>

</details>

<details>
  <summary> Please follow these steps to create IP pools (NIC\RFC) </summary>

  ```shell
  - creation on network containers for IP pools for NIC/RFC on Infoblox 
  ```

  Naming convention for containers description: <br />
  (proper format of description is needed to correctly support SSR 187 - one-demand network creation) <br />
  **locationCode:US-IRVLD;networkType:NIC;tenant:Siemens**

- locationCode – country and location code for a specific site
- networkType – NIC(routable) or RFC(private ) IP address ranges
- tenant – tenant name received from SNOW

  All details steps are included in the following documentation <br />
  <https://github.com/MAN-SFA/vRA-Documentation/blob/develop/wiAddEditRemoveNsxTNetwork.md>

</details>
