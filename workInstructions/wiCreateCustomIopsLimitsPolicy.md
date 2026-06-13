# Table of Contents

- [Table of Contents](#table-of-contents)
- [List of Changes](#list-of-changes)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
  - [Audience](#audience)
  - [Scope](#scope)
- [Related Documents](#related-documents)
- [customIops.csv file detailed description](#customiopscsv-file-detailed-description)
  - [File location](#file-location)
  - [List of Ansible Playbooks using customIops.csv](#list-of-ansible-playbooks-using-customiopscsv)
  - [file content](#file-content)
- [How to use csv file](#how-to-use-csv-file)
  - [while running playbook in manage phase](#while-running-playbook-in-manage-phase)
  - [While running playbook in update phase](#while-running-playbook-in-update-phase)

# List of Changes

| Version | Date | Author | Changes |
|---------|------|------|---------|
| 0.1 | 27.08.2021 | Document creation | Shilpa Arote |
| 0.2 | 2022-02-17 | DHC-3936 updated creation of high Iops storage profile creation | Shilpa Arote |

## Introduction

### Purpose

Pass custom IOPS limits and custom policy names for creation of new storage policies in vCenter and map them in vRA storage profiles.

## Audience

- VCS Operations
- VCS Engineering

## Scope

This document covers the following topics

- customIops.csv file preparation
- customIops.csv file usage

# Related Documents

| Document |
| -------- |
| lldStorageClassesIOPSLimits.md |
| lldServiceCatalog.md |
| wiLifeCycleManagement.md |
| wiAddVcfCluster.md |

# customIops.csv file detailed description

## File location

Customer's customIops.csv file will be stored in the home directory of the user who is executing the ansible yml playbooks.

## List of Ansible Playbooks using customIops.csv

- dhc/manage/addVcfCluster.yml
- dhc/manage/addClusterForTenant.yml
- dhc/update/upgradeSpbmPolicy.yml

## file content

customIops.csv file is used by playbooks in manage phase and update phase which are listed in above section *'List of Ansible Playbooks using customIops.csv'*
It is important to understand that customIops.csv file is not created as part of any of ansible playbooks that are using it, customIops.csv file needs to be created and updated before execution of ansible playbooks. Detailed headers description can be found below.
Create csv file with headers '**policyName,iopsLimit**'. Please avoid entering default storage policy name 'gold' and 'database'  also entered policy names and iops value should be unique.

              example file content-
              ---------------------------------------------------------------------------------------------------------------------------------
              policyName,iopsLimit
              fast,3500
              medium,2500
              low,1500
              -------------------------------------------------------------------------------------------------------------------------
              -------------------------------------------------------------------------------------------------------------------------
              -------------------------------------------------------------------------------------------------------------------------

# How to use csv file

## while running playbook in manage phase

The playbook *addVcfCluster.yml* needs to be run in order to form a new cluster in manage phase. The playbook performs following actions:
Creates a new cluster in SDDC Manager based on the input
Creates vSAN SPBM policies as defined in the **dhc-createSpbmPolicy** role
The playbook requires a number of inputs in order to create the cluster according to the requirements. Out of many user inputs one is for customIops.csv file if user decide to create custom vSAN SPBM policies instead of VCS defaults.

| Input/Variable | Description |
| -------- | ------- |
| csvFile | A csv file to provide inputs for custom iops limit values and policy names. It will be used to create new additional custom storage policies for newly created cluster. |

The playbook *addClusterForTenant.yml* is used for enabling the new cluster for a given tenant. It performs followings actions:

- Create a tenant-dedicated Resource Pool in the vSphere cluster
- Assign tags to the Resource Pool and Cluster in vRA
- Create vRA Storage Profiles in the tenant organization
- Update the blueprint in a given project
- Update the Service Broker form in a given project

Out of many inputs following input is asked for custom Iops limit:

| Input/Variable | Description |
| -------- | ------- |
| csvFile | Provide same csv file which you used for *addVcfCluster.yml* to maintain consistency of policy names and iops values. It will be mapped with vRA custom storage profiles for newly created cluster. |

Once csv file is ready, execute playbook using work instruction [wiAddVcfCluster](./wiAddVcfCluster.md#step-2---create-a-cluster)

## While running playbook in update phase

The playbook *upgradeSpbmPolicy.yml* is used for upgrading SPBM storage policies. It performs followings actions:

- Add new default policy as a gold storage
- Add high Iops storage policy as a database
- creates additional storage policies if user input is provided
- Maps new policies to new vRA Storage Profiles in the tenant organization
- Updates the blueprint in a given project
- Updates the Service Broker form in a given project

Out of many inputs following input is asked for custom Iops limit:

| Input/Variable | Description |
| -------- | ------- |
| csvFile | A csv file to provide inputs for custom iops limit values and policy names. It will be used to create new additional custom storage policies for newly created cluster. |

Once csv file is ready, execute playbook using work instruction [wiLifeCycleManagement](./wiLifeCycleManagement.md#update-vsan-storage-policies)
