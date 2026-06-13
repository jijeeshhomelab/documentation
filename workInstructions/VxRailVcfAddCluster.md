# VxRail VCF Add Cluster

- Table of Contents
{:toc}

# Changelog
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 10/04/2020 | First version | Łukasz Stasiak|

# 1. Introduction

## 1.1 Purpose

The purpose of this document is to describe steps that should be performed to add additional cluster to existing VCF workload domain on VxRail.

## 1.2 Scope

The scope of this document covers the following:

1. VxRail Manager initializing procedure.
2. New VxRail cluster configuration.
3. Steps to add created VxRail cluster to the existing vCF workload domain.

# 2. VxRail Manager initializing procedure

Initializing VxRail Manager is described in the ['VxRailManagerInitialization'](VxRailManagerInitialization.md) document.

In order to initialize VXRail Manager follow the steps described in chapter 'VxRail Manager initializing procedure'.

# 3. VxRail cluster configuration

VxRail cluster configuration  is described in the ['VxRailManagerInitialization'](VxRailManagerInitialization.md) document.

As a prerequisite for VxRail cluster creation process, PEQ file has to be prepared and filled up.
The VxRail Pre-Engagement Questionnaire (PEQ) is a excel file, enabling users to document the installation
parameters. The PEQ is intended to generate a VxRail appliance JSON to perform the installation and configuration of the VxRail appliance.
>NOTE!!! Remember to use proper PEQ excel sheet: 'VI 1 Cluster 2' and set 'vCenter & Credentials' and 'Platform Services Controller' sections as below on screenshot (marked in red).

![Figure 1](images/VxRailCreateWorkloadDomain/1.png)

In order to create and configure new compute VxRail cluster follow the steps described in chapter 'VxRail cluster configuration'.

# 4. Rename Datastore

Right click on the datastore object for the created cluster and select 'Rename'.
Provide the name according to VCS naming convention described in namingConvention.md e.g.  `gre07-c01-vsan02`

# 5. Rename VDS

Right click on the VDS object and select 'Rename'.
Provide the name according to VCS naming convention described in namingConvention.md e.g.  `gre07-c01-vds02`

# 6. Steps to add created VxRail cluster to the existing vCF workload domain

After VxRail cluster creation it needs to be added to the existing VCF workload domain.
Adding the VxRail Cluster to VCF workload domain is described in [VMware Cloud Foundation on Dell EMC VxRail Admin Guide](vcfOnVxrailAdministering.pdf) document.

In order to add the created VxRail cluster to VCF workload domain follow chapter 12. 'Expand a Workload Domain', step 'Add the VxRail Cluster'.
