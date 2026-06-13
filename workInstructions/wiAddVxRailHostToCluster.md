# Add VxRail Host to Cluster

# Table of Contents

<!-- TOC -->
- [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
- [Introduction](#introduction)
- [Scope](#scope)
- [Related Documents](#related-documents)
- [Assumptions](#assumptions)
- [Imaging or Resetting to factory settings the VxRail nodes](#imaging-or-resetting-to-factory-settings-the-vxrail-nodes)
- [Infrastructure Requirements](#infrastructure-requirements)
- [Software Requirements](#software-requirements)
- [Network Requirements](#network-requirements)
- [Procedure](#procedure)
  - [Step 1 - Add VxRail host to existing VxRail cluster in vCenter](#step-1---add-vxrail-host-to-existing-vxrail-cluster-in-vcenter)
  - [Step 2 - Add VxRail ESXi host in VCF management or workload domain](#step-2---add-vxrail-esxi-host-in-vcf-management-or-workload-domain)
  - [Step 3 - Compliance and Hardening tasks](#step-3---compliance-and-hardening-tasks)
  - [Step 4 - Add hosts to the DFW security group](#step-4---add-hosts-to-the-dfw-security-group)
  
# Changelog

| Date    | TOS        |  Issue             | Author          | Description          |
| ------- | ---------- | ------------------------ | --------------- | --------------- |
| 30/05/2022 | DHCVXR1.0 | First version | Arun Sompura |  Initial document |

# Introduction

This document describes the step-by-step instructions and low level configuration details for adding a new vxrail host to an existing VCF cluster. This document is intended for the DevSecOps engineers tasked with implementing it.

# Scope

Adding a new vxrail host to the existing cluster includes the 2 main areas:

- Adding VxRail host to existing VxRail cluster in vCenter.
- Adding VxRail ESXi host in VCF management or workload domain.

# Related Documents

| Document |
| -------- |
| [VCS Infrastructure LLD](../design/lldInfrastructure.md) |
| [VCS VxRail ESXi Factory reset Procedure](../workInstructions/dhcVxRailFactoryReset.md) |

# Assumptions

There is an assumption that the engineers following this process have an understanding of VMware VCF on VxRail.  
The assumption is that the ESXi hosts are already prepared and factory reseted and ready to be commisioned.  
All playbooks mentioned in this document are located in the *manage* folder in the GIT repository.

**DISCLAIMER!** All screenshots are for illustrative purposes only.

# Imaging or Resetting to factory settings the VxRail nodes

If VxRail hosts are not factory reseted, all VxRail hosts which needs to be added to the cluster have to be imaging or resetting to factory settings by using Dell EMC RASR (Rapid Appliance Self Recovery) process.
The procedure is covered in a document for 'VxRail 7.0.241 Nodes'

[VCS VxRail ESXi Refreshing procedure](../workInstructions/dhcVxRailFactoryReset.md).

# Infrastructure Requirements

Minimum 1 VxRail vSAN ready cluster present.
VCF workload(Management) domain and cluster must be configured already.

# Software Requirements

ESXi hosts build number needs to match the current VCF on VxRail Workload Domain level.
Newly added host should match same Firmware and drivers version as other hosts within cluster.

# Network Requirements

The VxRail hosts need to be located in the same Management network as the vCenter and SDDC Manager, VxRail discovery. SSH and HTTPS connectivity from the Ansible host to ESXi, vCenter, SDDC Manager, Hashivault and vRA Cloud needs to be available.

# Procedure

Depending on the domain type Management, or VI workload differnt role will get called as a part of automation from main playbook.

## Step 1 - Add VxRail host to existing VxRail cluster in vCenter

First step of adding a new vxrail host to the cluster is to an import ESXi hosts into VxRail cluster in vCenter so that it can be consumed and added in SDDC by the VCF addVxRailHosts workflow.  
Hosts are specified in **addMgmtWldVcfHostsVars.yml** file located in **/home** directory of a user that runs the playbook.  
The **addMgmtWldVcfHostsVars.yml** file should be placed in the user home directory and needs to have a dictionary variable containing information about the name and the octet for all new vxrail ESXi hosts.

For adding a vxrail host to either a standalone cluster or the primary site or a secondary site please refer to the example below:

```yaml
esxiHost:
  cmp008:
    name: "gre02cmp008"
    octet: 118
    cidr: "172.22.128"
    psnt: "DE600193105042"
```

A different naming convention will be used for the secondary site. Please refer to the following example:

```yaml
esxiHost:
  drcmp008:
    name: "gre32cmp008"
    octet: 118
    cidr: "172.22.40"
    psnt: "DE600193105035"
 ```

PSNT information can be fetched manually using KB article - This can be done manually by following the DELL's KB article - [Determining VxRail hosts PSNT Method 4 (000197326)] [https://www.dell.com/support/kbdoc/en-us/000197326/dellemc-vxrail-how-to-get-node-psnt](https://www.dell.com/support/kbdoc/en-us/000197326/dellemc-vxrail-how-to-get-node-psnt)

The playbook *addMgmtWldVxRailVcfHosts.yml* is adding a set of hosts to VxRail vCenter cluster using VxRail REST API.

The playbook contains 4 main parts:

1. Updating DNS entries for new vxrail ESXi hosts and gather appropriate input variables (file addMgmtWldVcfHostsVars.yml and user prompts about ESXi root and ESXi management password for VxRail)
2. Update the inventory (hosts) and "group_vars/all" files with the new entries for the additional hosts.
3. Require Cluster number on which vxrail hosts to be added.
4. VxRail manager name for configuration.

Apart from the username and password required for accessing Hashivault, the *addMgmtWldVxRailVcfHosts.yml* playbook requires the following inputs:

| Input/Variable | Description |
| -------- | ------- |
| esxPass | Password for the ESXi hosts. It needs to be uniform across all hosts which are being added |
| esxMgmt | Password for the ESXi hosts management. It needs to be uniform across all hosts which are being added |
| hostSite | The Site/Availability Zone for the new hosts. For a Standalone cluster, the value should be set to "primary". |
| hostType | The ESXi host type - either Compute or Management. Possible values - cmp/mgt. |
| workloadClusterNamePrompt | As there may be more than one workload clusters in VI workload domain. This value is set by default to "locationCode-c01-cluster01" |
| clusterNumber | By default it's set to "01" because the first cluster is deployed during the initial build in management or workload. |
| hostNumbers | Number of vxrail ESXi hosts that will be added to the domain / cluster. The number needs to match the maximum allowed as per vendor. By default this variable is set to 1. |  
| vmnic1Id | The 1st VMnic number that will be used in the distributed switch. By default this is set to 0. It needs to be uniform across all hosts in the cluster |
| vmnic2Id | The 2nd VMnic number that will be used in the distributed switch. By default this is set to 1. It needs to be uniform across all hosts in the cluster |
| esxiLicenseKey | A valid vSphere license key. By default the value is taken from group vars. **Make sure that it's a valid one before accepting the default value**  |
| vsanLicenseKey | A valid VSAN license key. By default the value is taken from group vars. **Make sure that it's a valid one before accepting the default value**  |
| vxm | A vxrail manager inputs for VxRail manager name default **vxm001** for 1st primary VxRail management cluster. |

## Step 2 - Add VxRail ESXi host in VCF management or workload domain

Once the ESXi hosts have been successfully commissioned in VxRail vCenter cluster and are visible in VxRail host inventory, the playbook *addMgmtWldVxRailVcfHosts.yml* needs to be run in order to add a given host to an existing domain and cluster (either Compute or Management).
The playbook performs the following actions:

- Get ESXi ssh thumbprint which wiill be used while Adding the host to a given domian and cluster
- ESXi root user and password.
- Hostname and IPaddress.

The playbook requires a number of inputs in order to add a host to a domain cluster according to the requirements. Apart from the username and password required for accessing Hashivault, the required inputs are already covered under Step 1:

## Step 3 - Compliance and Hardening tasks

In order to make sure that the new hosts are joined to domain, hosts passwords are rotated and updated in vault.
**NOTE:** syslog log insight configuration is already being done during VxRail bringup task for ESXi.

Description:
The playbook addMgmtWldVxRailVcfHosts.yml* contains sub-tasks which gets trigerred by the playbook as the post host addition to VCF domain / cluster.

- Add hosts to domain.
- Rotate ESXi hosts root password and update in vault.
- Rotate ESXi management password update in vault.
- Updated ESXi management password in respective VxRail manager.

```yaml
- name: Execute Task to add newly added host to Domain
  import_tasks: addHostsToDomain.yml
  
- name: Execute rotate password for newly added host from SDM
  import_tasks: rotatePasswords.yml
  
- name: Update random ESXi management password for newly added host
  import_tasks: updateRandomPasswordESXi.yml
  with_items: "{{ newHostList }}"
  
- name: Generate random management Password and update in vault
  include_role:
     name: dhcvxr-addVxRailHostToVcfMgtCluster
     tasks_from: updateRandomPasswordESXi.yml
  with_items: "{{ newHostList }}"
  
- name: Get serial numbers of ESXi hosts in cluster
  import_tasks: getSerialNumber.yml
  
- name: Get VxRail manager credentials and update management password of added esxi inot VxRail manager
  import_tasks: getVxrailCredentials.yml

```

## Step 4 - Add hosts to the DFW security group

The new hosts will be added to existing security group in NSX-T.

Description:
The playbook *addMgmtWldVxRailVcfHosts.yml* contains role and further sub-tasks which gets trigerred by the playbook  to add added hosts to a NSX-T security group.

Below sub-task will add host(s) to NSX-T security group.

```yaml
- name: Add VxRail host to NsxSecurity group of Management domain
  import_tasks: addVxrailHostsToNsxSecurityGroup.yml
  
- name: Add VxRail host to NsxSecurity group of Management domain
  import_tasks: addVxrailHostsToNsxSecurityGroup.yml
  
```
