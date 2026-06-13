# CloudLink to vSphere Native Key Provider

## Changelog

| Date | Issue | Author | Description |
| -----|-------|--------|------------- |
| 06.28.2024 | VCS-12855  | Nicu Butaru | Document Creation |

## Introduction

### Purpose

Create and configure a vSphere Native Key Provider and change existing vSAN encryption from existing standard key provider to vSphere Native Key Provider (NKP) for environments with `vSphere 7 update 2 or newer` .

### Audience

- VCS Engineering
- VCS Operations

### Scope

This document covers the following tasks and activities:

- Configure NKP
- Enable vSAN encryption
- Shutdown KMS cluster
- Testing

## Deployment steps

### vSphere Native Key Provider configuration - manual steps

1. Log in to the vCenter Server system with the vSphere Client ( for vCenters in Enhanced Linked Mode you need to be logged in to the targeted vCenter where you want to configure NKP ).
2. Browse the inventory list and select the vCenter Server instance.
3. Click `Configure`, and under `Security` click `Key Providers`.
4. Click `Add` then click `Add Native Key Provider`.
5. Enter a name for the vSphere Native Key Provider ( ex: "{{ vcenter }}-NKP-001" ).
6. Do not mark `Use key provider only with TPM protected ESXi hosts (Recommended)` unless required.
7. Click `Add Key Provider`.
8. Select the vSphere Native Key Provider\
A status of "Not backed up" appears for key providers that you have not backed up.
9. Click `Back Up`\
To password-protect the backup, check the `Protect Native Key Provider data` with password box.\
Enter a password and save it in a secure location ( HashiVault under vCenter entry ).
Check the `I have saved the password in a secure place` box, indicating that you have saved the password to a secure place.
10. Click `Back Up Key Provider`\
The backup file is in PKCS#12 format.\
Use: `# certutil -encode ExportBackup.p12 encoded_ExportBackup.txt` and save the content of encoded_ExportBackup.txt to Vault under vCenter entry .\
The status of the vSphere Native Key Provider changes from `Not Backed Up`, to `Active`.
11. Select the vSphere Native Key Provider and click `Set as Default` .

### vSphere Native Key Provider configuration - automated in Manage

The process of craeating, configuring NKP and set is as default is automated by ansible role dhc-configureNativeKeyProvider launched by createNativeKeyProvider.yml playbook. Whole configuration process is a part of Manage phase.
A result of this playbook is an instance of NKP configured in target vCenter Server with a configuration backup stored in HashiVault and set as default in targeted vCenter .\
Please run: # ansible-playbook createNativeKeyProvider.yml from Manage .\
ex: a580621@gre28ans001:~/`dhc/manage`$ `ansible-playbook createNativeKeyProvider.yml` \
Once configuration process is completed successfully, vSAN encryption can be enabled.

### Enable vSAN Encryption CMP cluster

1. Navigate to the vSAN cluster.
2. Click the `Configure` tab.
3. Under `vSAN`, select `Services`.
4. Click the `Encryption || Data Services`  Edit button.\
On the vSAN Services dialog, under `Encryption`, and from Key provider dropdown select the created provider (nkp)
5. Click `Apply`.
6. Check the progress of starting tasks in Monitor > Tasks

### Shutdown KMS cluster

Power off KMS VM's

### Testing

After shuting down KMS VM's we can test if the new native key provider is in use by placing a host in maintenance mode with ensure accessibility and reboot it . After the host is back online data should be accessible and vSAN health check `Data-at-rest encryption` should be green .\
Select Cluster > Monitor > vSAN > Skyline Health to check Overall Health .\
Once testing is successful you can delete the Standard Key Provider .\
Browse the inventory list and select the vCenter Server instance.\
Click `Configure`, and under `Security` click `Key Providers`.
Select the key provider you want to delete.
Click `Delete`.\
Read the warning message and slide the slider all the way to the right.
Click `Delete`.

## Restoring NKP from HashiVault

In case a NKP instance has to be recovered following steps have to be completed.

| Step       | Action     |
| :------------- | ---------- |
|1. | In Vault navigate to vCenter Server instance on which NKP was configured, ie. < locationCode>vcs002 and copy content of `.p12` entry, ie. `gre28vcs002-NKP-0012024-07-03T10:22:19.548358Z.p12` |
|2. | Save content in .txt file |
|3. | Decode file to `.p12` file. On Linux systems use `base64 -d source_file.txt > target_file.p12`. On Windows system `certutil -decode source_file.txt target_file.p12`|
|4. | From vSphere Web client navigate to vCenter instance on which NKP needs to be restored, go to `Configure>Security>Key Providers`, select `Restore` and select `.p12` file generated in previous step. Provide `encryption password` from HashiVault, ie: `gre28vcs002-NKP-0012024-07-03T10:22:19.548358Z.p12 encryption password`|
|5. | Click `Next` and `Finish`. Do not mark `Use key provider only with TPM protected ESXi hosts (Recommended)` unless required.|
|6. | Refresh page, at this stage NKP is restored and NKP status should be `Active`|
