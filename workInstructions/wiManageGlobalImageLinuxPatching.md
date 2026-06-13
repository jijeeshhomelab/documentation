# Manage Global Image Patching - Linux

## Table of Contents

- [Manage Global Image Patching - Linux](#manage-global-image-patching---linux)
  - [Table of Contents](#table-of-contents)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Prerequisites](#prerequisites)
  - [Patching Steps](#patching-steps)
  - [Registration data](#registration-data)
    - [RHEL](#rhel)
    - [SUSE](#suse)

## Changelog

| Version | Date | User | Changes |
|---------|------|------|---------|
| 0.1 | 17.06.2022 | Krystian Bibik | initial version |
| 0.2 | 14.07.2025 | Stanislaw Kilanowski | Updated process for RHEL templates |

## Introduction

### Purpose

Patch Linux OS global images in workload domain.

### Audience

- VCS Operations

### Scope

This document covers the following activities:

- Automated Image Patching
- Manual verification
- Manual troubleshooting

Currently supported operating systems:

- Red Hat Enterprise Linux 7 (RHEL7)
- Red Hat Enterprise Linux 8 (RHEL8)
- SUSE Linux Enterprise Server 15 (SLES15)

## Prerequisites

- The desired Global Image exists in the workload domain published content library (locationCode-p-cl01).
- There is enough space on target vSAN to deploy the VM
- Operator uses their personal management domain account (account creation is a part of VCS hardening)
- The required subscription to register RHEL in Red Hat Customer Portal
- The required subscription to register SLES in SUSE Customer Center
- The required credentials to the target global image are located in Hashicorp Vault

## Patching Steps

By default VCS supports three Global Images linux servers templates:

Red Hat Enterprise Linux 7 (use `-e templateName=GlobalImage_RHEL7.6`),  
Red Hat Enterprise Linux 8 (use `-e templateName=GlobalImage_RHEL8.1`)  
and SUSE Linux Enterprise Server 15 (use `-e templateName=GlobalImage_SLES15`).

Execute the following playbook from the */opt/dhc/manage* folder.

To update Operation System and cloud-init package of the the specified template in the Content Library use playbook without tags.

Execute:

```bash
ansible-playbook updateLinuxImages.yml -i hosts -e templateName=<template name here>
```

To update only cloud-init package use playbook with `--skip-tags patch`.

Execute:

```bash
ansible-playbook updateLinuxImages.yml -i hosts -e templateName=<template name here> --skip-tags patch
```

> Playbook:
> >
> - prompts for user dasId from the VCS management domain in format `dasId@domain.next`
> - prompts for SLES registration credentials: SCC account name and registration code (if needed)
> - obtains root CA cert and stores it inside variable for further usage
> - clones the < templateName > VM from content library into compute vCenter and converts it as a temporary virtual machine
> - powers on the temporary virtual machine and executes the VM configuration tasks
> - executes registration, locks minor release version (RHEL) and excludes cloud-init package from updating
> - executes tasks to start process of downloading end installing missing patches for specified minor release version (e.g. RHEL7.6, RHEL8.1)
> - executes tasks to store patching report and copy to the deb001 machine
> - executes task to unconfigure VM
> - updates fixed version of cloud-init package and adds configuration fixes (cloudnetdisable, cloudfix and cloudfixUser)
> - exports the VM to OVF to < ofvExportDir > folder
> - executes task to update content library with exported ovf
> - removes the temporary template VM
> - removes exported OVF

## Registration data

As part of the Global Linux Image patching, we register the RHEL/SLES operating system, perform the patching and unregister it.

### RHEL

For RHEL templates the playbook requires data for a Red Hat Registration: an activation key and the organization ID. They are saved in the related role's defaults, however the variables may be overwritten with extra vars if needed. In order to obtain the details follow the below process:

1. Log in to [Red Hat Console](https://console.redhat.com) with the DevSecOps DAS Functional Account `vcs-automation`.
2. Navigate to the [Registration Assistant](https://console.redhat.com/insights/registration).
3. Select an existing Activation key or create a new one (if needed).
4. Select the operating system
5. A command for the Subscription Manager will be generated. Copy the `activationkey` and `org` fields from it.

The default values can be overwritten by executing the following playbook:

```shell
ansible-playbook updateLinuxImages.yml -i hosts -e 'templateName=<template name here> rhKeyId=<activationkey> rhOrgId=<org>'
```

### SUSE

When the playbook is launched for the SLES template with the update Operation System and cloud-init package option selected, the user will be prompted for OS registration data: **SUSE Customer Center account name (e-mail) and registration code**.
