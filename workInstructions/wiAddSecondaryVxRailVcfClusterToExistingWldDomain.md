# VxRail VCF Add Secondary Cluster to Existing Workload Domain

# Table of Contents

<!-- TOC -->
- [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
- [Introduction](#introduction)
- [Scope](#scope)
- [Related Documents](#related-documents)
- [Assumptions](#assumptions)
- [Infrastructure Requirements](#infrastructure-requirements)
- [Software Requirements](#software-requirements)
- [Network Requirements](#network-requirements)
- [Pre-Implementation Check List](#pre-implementation-check-list)
  - [IP address uniqueness](#ip-address-uniqueness)
  - [IP address availability](#ip-address-availability)
  - [Firmware version appropriateness](#firmware-version-appropriateness)
- [Procedure](#procedure)
  - [Step 1 - VxRail Manager initializing procedure](#step-1---vxrail-manager-initializing-procedure)
  - [Step 2 - VxRail Secondary cluster configuration and Bringup](#step-2---vxrail-secondary-cluster-configuration-and-bringup)
  - [Step 3 - Overall Steps for VxRail cluster addition to vcfWorkload domain](#step-3---overall-steps-for-vxrail-cluster-addition-to-vcfworkload-domain)
  - [Step 4 - Input and Variables with Description](#step-4---input-and-variables-with-description)
- [Compliance and Hardening](#compliance-and-hardening)
  
# Changelog
  
| Date    | TOS        |  Issue             | Author          | Description          |
| ------- | ---------- | ------------------------ | --------------- | --------------- |
| 10/05/2022 | DHCVXR1.0 | First version | Arun Sompura |  Initial document |

# Introduction

This document describes the step-by-step instructions and low level configuration details for adding a new secondary vxrail cluster to an existing VCF workload. This document is intended for the DevSecOps engineers tasked with implementing it

## Purpose

The purpose of this document is to describe steps that should be performed to add an additional cluster to existing VCF workload domain on VxRail.

## Scope

The scope of this document covers the following:

1. VxRail Manager initializing procedure for new Cluster.
2. New VxRail cluster configuration and Bringup.
3. Steps to add created VxRail cluster to the existing vCF workload domain using playbook.

## Related Documents

| Document |
| -------- |
| [VCS Infrastructure LLD](../design/lldInfrastructure.md) |
| [VCS VxRail VxRailManager Initalization Procedure](../workInstructions/dhcVxRailManagerInitialization.md) |
| [VCS VxRail ESXi Factory reset Procedure](../workInstructions/dhcVxRailFactoryReset.md) |

# Assumptions

There is an assumption that the engineers following this process have an understanding of VMware VCF on VxRail.  
The assumption is that the ESXi hosts used for forming the new VxRail cluster are factory reseted and ready to be bringup.
There is Pre-existing management and 1 worklolad cluster in SDDC.
All playbooks mentioned in this document are located in the *manage* folder in the GIT repository.

# Infrastructure Requirements

Minimum 1 VxRail vSAN ready VI workload cluster present in SDDC to be able to create a secondary workload cluster.
Minimum 3 VxRail vSAN ready nodes are required for secondary cluster creation.

# Software Requirements

VxRail hosts build number needs to match the current VCF VxRail Workload Domain level.

# Network Requirements

The ESXi hosts need to be located in the same Management network as the vCenters and SDDC Manager. SSH and HTTPS connectivity from the Ansible host to ESXi, vCenter, SDDC Manager, Hashivault and vRA Cloud needs to be available.

Please refer to the **lldSoftwareDefinedNetworks.md** document for details regarding network requirements.

# Pre-Implementation Check List

In order to ensure a successful addition of a new cluster a number of pre-checks must be performed.

## IP address uniqueness

Make sure the IP addresses for the management (vmk0) interfaces do not overlap with the IP addresses of the existing hosts.

This is a manual check on vCenter level done through GUI by selecting a host and navigating to **Configure** tab -> **Networking** -> **VMkernel adapters**; or using PowerCLI to obtain the vmk0 IP information for each host.

### IP address availability

Check whether the network pool has sufficient number of free IP addresses in order to add new hosts

### Firmware version appropriateness

Check whether the firmware version of the new hosts matches the firmware of the already present hosts.

# Procedure

## Step 1 - VxRail Manager initializing procedure

a.) VxRail manager initalization and Factory reset procedure is given below.

[VCS VxRail VxRailManager Initalization Procedure](../workInstructions/dhcVxRailManagerInitialization.md)

[VCS VxRail ESXi Factory reset procedure](../workInstructions/dhcVxRailFactoryReset.md)

b.) Upon Factory reset completion VxRail manager for Secondary cluster will be available for VxRail cluster bringup.

## Step 2 - VxRail Secondary cluster configuration and Bringup

VxRail secondary cluster configuration and addition into an existing VCF VxRail workload domain needs the pre-requsites document to be checked and understood by team member performing cluster addition.

 *Note! Additional Active-Passive DR VxRail vCF clusters are not supported in the current release**

ReadMe file :

`/opt/dhcvxr/manage/roles/dhcvxr-addAdditionalVxRailClusterInWorkloadDomain/addSecondaryClusterToExistingWorkloadDomainREADME.md`

Hosts details with IP and names are specified in addVxRailHostsVars.yml file located in /home directory of a user that runs the playbook.  
The **addVxRailHostsVars.yml** file should be placed in the user home directory and needs to have a dictionary variable containing information about the name and the octet for all new ESXi hosts including PSNT number for VxRail nodes.  
Please refer to the example below:

```yaml
esxiHost:
  cmp008:
    name: "gre02cmp008"
    octet: 118
    cidr: "172.22.128"
    psnt: "DE600193105042"
  cmp009:
    name: "gre02cmp009"
    octet: 119
    cidr: "172.22.128"
    psnt: "DE600193105043"
  cmp010:
    name: "gre02cmp010"
    octet: 120
    cidr: "172.22.128"
    psnt: "DE600193105044"
    cidr: "172.22.40"
```

PSNT information can be fetched manually using KB article -
This can be done manually by following the DELL's KB article - [Determining VxRail hosts PSNT Method 4 (000197326)]
 [https://www.dell.com/support/kbdoc/en-us/000197326/dellemc-vxrail-how-to-get-node-psnt](https://www.dell.com/support/kbdoc/en-us/000197326/dellemc-vxrail-how-to-get-node-psnt)

The playbook **dhcvxr-additonalClusterToWorkloadDomain.yml**  performes VxRail bringup to vSphere workload vCenter then performs post VxRail bringup tasks and then vCF VxRail bringup in workload domain inventory using SDDC Manager REST API

## Step 3 - Overall Steps for VxRail cluster addition to vcfWorkload domain

The playbook contains below parts:

1. Create or update DNS entries for new ESXi hosts and gather appropriate input variables (file **addVxRailHostsVars.yml** and user prompts about ESXi root password)
2. Updates the inventory (hosts) and "group_vars/all" files with the new entries for the additional hosts.
3. Bringup the new ESXi hosts and create a secondary VxRail cluster in workload vCenter.
4. Perfroms vDS rename, vSAN rename and vSAN storage policy for newly built secondary VxRail cluster.
5. Performs Vcf bringup for secondary VxRail cluster to existing workload domain use ESXi hosts and VxRail manager thumbprint.
6. Add hosts to domain and Rotate ESXi hosts password and update in vault.

Apart from the username and password required for accessing Hashivault, the *addVxRailHostsVars.yml* playbook requires the following inputs:

## Step 4 - Input and Variables with Description

| Input/Variable | Description |
| -------- | ------- |
| esxPass | Password for the ESXi hosts. It needs to be uniform across all hosts in the addVxRailHostsVars.yml file |
| esxMgmt | auto generated |
| hostSite | The Site/Availability Zone for the new hosts. For a Standalone cluster, the value should be set to "primary". |
| hostType | The ESXi host type - either Compute or Management. Possible values - cmp/mgt. In this case, only "cmp" is a valid value, as we are creating a Compute cluster. It is the default value in the playbook |
| workloadDomainNumber | At the moment only a single Workload Domain is supported in DHCVXR. This value is set by default to "01"|
| clusterNumber | By default it's set to "02" because the first cluster is deployed during the initial build. |
| numberOfHostsForAdditionalCluster | Number of ESXi hosts that will be used to create the cluster. The number needs to match the number of hosts commissioned in the previous step. By default this variable is set to 4. |  
| availabilityZone |  By default this variable is set to match the locationCode. It ensures that only the unassigned hosts from a given location are taken into account. |
| vmnic1Id | The 1st VMnic number that will be used in the distributed switch. By default this is set to 0. It needs to be uniform across all hosts in the cluster |
| vmnic2Id | The 2nd VMnic number that will be used in the distributed switch. By default this is set to 1. It needs to be uniform across all hosts in the cluster |
| esxiLicenseKey | A valid vSphere license key. By default the value is taken from group vars. **Make sure that it's a valid one before accepting the default value**  |
| vsanLicenseKey | A valid VSAN license key. By default the value is taken from group vars. **Make sure that it's a valid one before accepting the default value**  |
| vxm | A vxrail manager inputs for VxRail manager name default **vxm003** for 1st secondary VxRail workload cluster. |

Below are the Roles which triggerd by the playbook **dhcvxr-additonalClusterToWorkloadDomain.yml** to add secondary cluster addition tasks.

## Step 5 - Playbook and Roles

Playbook **dhcvxr-additonalClusterToWorkloadDomain.yml**

**Roles** :

1. VxRail Bringup for secondary cluster in workload vCenter.

   ```yaml

    - name: "Running role: dhcvxr-createAdditionalWorkloadVxRailBringupFile"
      include_role:
        name: dhcvxr-createAdditionalWorkloadVxRailBringupFile

  
    - name: "Running role: dhcvxr-createAdditionalWorkloadVxRailBringup"
      include_role:
        name: dhcvxr-createAdditionalWorkloadVxRailBringup
   
   ```

2. Post VxRail tasks and pre-vcf bringup Tasks.

   ```yaml

    - name: "Running role: dhcvxr-createPostAdditionalClusterVxRailTasks"
      include_role:
        name: dhcvxr-createPostAdditionalClusterVxRailTasks

    - name: "Running role: dhcvxr-getEsxiSshThumbprintAdditionalCluster to start SSH service on ESX hosts"
      include_role:
        name: dhcvxr-getEsxiSshThumbprintAdditionalCluster
        tasks_from: startSSHCluster.yml

   ```

3. VcfVxRail cluster addition to existing workload domain.

  ```yaml

      - name: "Running role: dhcvxr-addAdditionalVxRailClusterInWorkloadDomainFile"
        include_role:
          name: dhcvxr-addAdditionalVxRailClusterInWorkloadDomainFile

      - name: "Running role: dhcvxr-addAdditionalVxRailClusterInWorkloadDomain to add cluster to existing WD"
        include_role:
          name: dhcvxr-addAdditionalVxRailClusterInWorkloadDomain
  ```
  
# Compliance and Hardening

Below are the post provisioning tasks which gets trigger by the playbook at the end. It takes care of hardening and compliance for newly built cluster.

- Manage certificate from SDDC manager for new VxRail
- Add hosts to domain
- Rotate ESXi hosts password and update in vault.
- Add cluster to DFW security group

  ```yaml

       - name: "Running role: dhcvxr-manageAdditionalVxRailClusterCertificates "
         include_role:
           name: dhcvxr-manageAdditionalVxRailClusterCertificates
           vars:
             workloadDomainNumber: "{{ workloadDomainNumber }}"
             clusterNumber: " {{ clusterNumber }}"
    ```

    Below sub-task will add cluster host(s) to NSX-T security group

    ```yaml

        - name: add ESXi hosts to NSX security group
          hosts: localhost
          gather_facts: false
          become: yes
          vars_files:
            - /home/{{ lookup('env', 'USER') }}/addVxRailHostsVars.yml
          tasks:
          - name: Include group_vars/all as this file has been modified in the previous play
            include_vars:
              file: ./group_vars/all

        - name: add ESXi hosts to NSX security group
          include_role:
            name: dhcvxr-addAdditionalVxRailClusterInWorkloadDomain
            tasks_from: addHostsToNsxSecurityGroup.yml
      ```
