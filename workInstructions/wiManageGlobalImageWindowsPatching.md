# Manage Global Image Patching - Windows

# Changelog

| Version | Date | User | Changes |
|---------|------|------|---------|
| 0.1 | 13.08.2020 | Kacper Kuliberda | initial version |
| 0.2 | 14.06.2021 | Robert Kaminski | DHC-1683 adopting patching steps description |

## Introduction

### Purpose

Patch Windows OS global images in workload domain.

### Audience

- VCS Operations

### Scope

- Automated Image Patching
- Manual verification
- Manual troubleshooting

In case additional info is needed, please refer to the 'windowsPatching.md' work instruction.
While the management server patching is not analogous to workload domain, it might prove helpful in case of issues.

Currently supported operating systems:

- Windows Server 2016
- Windows Server 2019

## 4. Prerequisites

- The desired Global Image exists in the workload domain published content library (locationCode-p-cl01).
- There is enough space on target vSAN to deploy the VM
- Windows Update server (wus001) is synchronized with Microsoft Update and the patches are approved (AutoApprove is enabled by default in VCS build).
- Operator uses their personal management domain account (account creation is a part of VCS hardening)
- The required credentials to the target global image are located in Hashicorp Vault

## 5. Patching Steps

Execute the following playbook from the */opt/dhc/manage* folder to patch the specified template in the Content Library:

```bash
ansible-playbook updateWindowsImage.yml -i hosts -e templateName=<template name here>
```

By default VCS supports two Global Images windows servers templates: Windows 2016 (use `-e templateName=GlobalImage_2016`) and Windows 2019 (use `-e templateName=GlobalImage_2019`).

> Playbook:
> >
> - prompts for user dasId from the VCS management domain in format `dasId@domain.next`
> - obtains root CA cert and stores it inside variable for further usage
> - [`create`] clones the < templateName > VM from content library into compute vCenter and converts it as a temporary virtual machine
> - [`create`] powers on the temporary virtual machine and executes the VM configuration tasks
> - [`patch`] executes WSUS configuration tasks required to validate the WSUS status
> - [`patch`] executes task to start process of downloading end installing missing patches
> - [`patch`] executes task to store patching report and copy to the WSUS machine
> - [`unconfigure`] executes task to unconfigure VM
> - [`clone`] exports the VM to OVF to < ofvExportDir > folder
> - [`clone`] executes task to update content library with exported ovf
> - [`clone`] removes the temporary template vm
> - [`clone`] removes exported ovf

**NOTE**: Due to the nature of Windows Update, especially if the target image is very outdated, not all patches may be applied in a single round. Therefore **it is recommended to execute the playbook using tags and run the `patch` tag until the image is fully updated**. Tags order must be followed.

Execute:

```bash
ansible-playbook updateWindowsImage.yml -i hosts -e templateName=<template name here> --tags <tagName>
```

The playbook can be executed with the following tag names, keep the order:

1. `create` - deploys the VM from content library and configure it with static IP and DNS configuration, as well as the required registry entries and security certificates
2. `patch` - triggers patching from WSUS (repeat the step as many time needed to have temporary VM fully patched, refer to `D:\AnsiblePatchReport\global_image_update' on *wus001* server)
3. `unconfigure` - restores the temporary VM configuration to defaults (removes network and WSUS configuration, prepares for cloning)
4. `clone` - powers the temporary VM off, exports VM to OVF, updates the existing template with a new image, deletes the VM and OVF.
