# Configure vSphere Native Key Provider

## Changelog
  
| Date | TOS       | Issue | Author       | Description       |
| ------- | ---------- | --------|------------------------ | --------------- |
| 07.02.2024 | VCS 2.0 | VCS-12032 | Adam Wieczorek | Document creation |

## Introduction

### Purpose

Install and configure a new instance of vSphere Native Key Provider (NKP) and vSAN encryption.

### Audience

- VCS Engineering
- VCS Operations

### Scope

This document covers the following tasks and activities:

- Automated NKP configuration
- Enabling vSAN encryption

### Installation Time

| Component / Task | Installation Time (HH:MM)    |
| :------------- | ---------- |
|  KMS deployment and configuration | 00:05   |
| Enabling vSAN encryption    | Depending on datastore capacity and VSAN configuration |

## Deployment steps

### Automated vSphere Native Key Provider configuration

The process of configuring NKP is fully automated by ansible role `dhc-configureNativeKeyProvider` launched by `createNativeKeyProvider.yml` playbook. Whole configuration process is a part of Deploy, stage 2 phase.  
A result of this playbook is an instance of NKP configured in target vCenter Server with a configuration backup stored in HashiVault.  
Once configuration process is completed successfully, vSAN encryption can be enabled.

### Enable vSAN Encryption CMP cluster

| Step       | Action     |
| :------------- | ---------- |
| 1.       | As encryption is CPU intensive, AES-NI needs to be enabled in BIOS of a vSAN nodes. Verify that this is already enabled.|
| 2.       | Log on to the vCenter server and select CMP cluster. Next click `Configure`. Under `vSAN`, select `Services` </br> Click the `Encryption` `Edit` button.|
| 3.       | On the vSAN Services window select the Encryption. Select created NKP instance <br> __Do not select__ *Wipe residual data* and *Allow Reduced Redundancy*</br>Click `Apply` to enable encryption.<br>__Note:__ If you have less than 30% free space on VSAN datastore then you can select *Allow Reduced Redundancy* option. This option keeps the VMs running, but the VMs might be unable to accept the full number of failures defined in the VM storage policy.  As a result, the virtual machine will be in risk of single point of failure.|
| 4.| Make sure that encryption is finished successfully. Overall Progress can be monitored in Monitor > tasks.|

## Restoring NKP from HashiVault

In case a NKP instance has to be recovered following steps have to be completed.

| Step       | Action     |
| :------------- | ---------- |
|1. | In HashiVault navigate to vCenter Server instance on which NKP was configured, ie. < locationCode>vcs002 and copy content of `.p12` entry, ie. `gre27vcs002-NKP-0012024-02-02T10:14:29.103195Z.p12` |
|2. | Save content in .txt file |
|3. | Decode file to `.p12` file. On Linux systems use `base64 -d source_file.txt > target_file.p12`. On Windows system `certutil -decode source_file.txt target_file.p12`|
|4. | From vSphere Web client navihate to vCenter instance on which NKP needs to be restored, go to `Configure>Security>Key Providers`, select `Restore` and select `.p12` file generated in previous step. Provide `encryption password` from HashVault, ie: `gre27vcs002-NKP-0012024-02-02T10:14:29.103195Z.p12`|
|5. | Click `Next` and `Finish`. Do not mark `Use key provider only with TPM protected ESXi hosts (Recommended)` unless required.|
|6. | Refresh page, at this stage NKP is restored and NKP status should be `Active`|
