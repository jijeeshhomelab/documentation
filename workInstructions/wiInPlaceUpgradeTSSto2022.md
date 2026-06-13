# Upgrade Windows Terminal servers to 2022

## Table of Contents

- [Upgrade Windows Terminal servers to 2022](#upgrade-windows-terminal-servers-to-2022)
  - [Table of Contents](#table-of-contents)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Pre-requisites](#pre-requisites)
  - [Automated part by using playbook](#automated-part-by-using-playbook)
    - [Role description](#role-description)
    - [Requirements](#requirements)
    - [Role variables](#role-variables)
    - [Dependencies](#dependencies)
  - [Post playbook execution steps](#post-playbook-execution-steps)
  - [Post upgrade actions](#post-upgrade-actions)
  - [Roll back actions](#roll-back-actions)
  - [Windows Remote Desktop (RDS CALs) for Terminal Server](#windows-remote-desktop-rds-cals-for-terminal-server)
  
## Changelog

|Date|TOS|Issue|Author|Description|
|-----|-----|-----|-----|-----|
|2024-03-20|DHC 1.8.3|VCS-11983|Ciprian Sferle|Initial draft|
|2025-03-11|DHC 1.8.3|VCS-15161|Krishnasai Dandanayak|Updated the RDS CAL Licenses details|
|2004-03-20|DHC 1.8.3|VCS-11983|Ciprian Sferle|Initial draft|
|2025-03-26|DHC 1.8.3|VCS-15498|Tomasz Korniluk|Update with network prerequisites based on ToS Bug story|

## Introduction

This document describes step-by-step instructions to upgrade Windows Terminal Servers to Windows 2022.

### Audience

DHC deployment engineers, Dev-Sec-Ops team

### Scope

The scope of this document is to cover the overall steps to upgrade a Windows Server to 2022

## Pre-requisites

- Hardware:
  - [ ] CPU: minimum 1.4 GHz 64-bit processor that supports NX and DEP, CMPXCHG16b, LAHF/SAHF, PrefetchW and  Second Level Address Translation (EPT or NPT);
  - [ ] Memory: minimum 512 MB for Server Core, 2 GB for Server with Desktop Experience, ECC (Error Correcting Code) type or similar technology for physical host deployments;
  - [ ] Storage: minimum 32GB of space (inside OS local drive);
  - [ ] Network: minimum an Ethernet adapter that can achieve a throughput of at least 1 gigabit per second, compliant with the PCI Express architecture specification;
  - [ ] Other requirements: UEFI 2.3.1c-based system and firmware that supports secure boot, Trusted Platform Module (TPM);
  [^1]
- Software
  - [ ] Windows Server 2022 Standard 64-bit iso file [^2]
  - [ ] Product key (MAK key)
  [^1]
- Network:

| Source component |  Destination component| Destination port number| Traffic role |
| ------------- | ----------------------- |--------- |--------- |
| Ansible node (ans001) | TSS server (tss001)| TCP 5985,5986 | WINRM communication to execute remote tasks inside Termninal Services Server |
| TSS server (tss001)|ICA,RCA servers (ica001,rca001)| TCP 135 | RPC communication for ICA certificates enrollment activities |
| TSS server (tss001)|ICA,RCA servers (ica001,rca001)| TCP 445 | Server Message Block (SMB) access to share |
  
> [!IMPORTANT]
>
> - Backup
>
>   - [ ] Check with the Backup team on latest backup status, if needed create new backup of the servers before the activity is started.
>   - [ ] Take snapshots for both TSS servers.
>

The in-place upgrade is a hybrid action using:

- Automated portion: configure proxy server, download and mount ISO image
- Manual Steps: post playbook actions to start the upgrade process

## Automated part by using playbook

```shell
ansible-playbook upgradeTssTo2022.yml
```

Following playbook is sample playbook which calls the role:

```bash
# tasks file for dhc-upgradeTssTo2022

- name: Configure Windows Server for upgrade to 2022
  hosts: localhost
  gather_facts: false

  vars_prompt:
    - name: windows_server
      prompt: "Enter the Windows server which you want to upgrade tss001 or tss002: "
      private: no

  tasks:
    - name: Store Windows Server Name in a Variable 
      set_fact:
        my_windows_server: "{{ windows_server }}"

    - name: Display Server Name
      debug:
        msg: "Configuring server {{ my_windows_server }}"

    - name: Add Windows Server to Dynamic Inventory Name
      add_host:
        name: "{{ my_windows_server }}"
        groups: my_dynamic_group
     
- name: "Please enter Windows Credentails"
  hosts: my_dynamic_group
  gather_facts: false
  vars_files:
    - group_vars/all
  vars_prompt:
    - name: username
      prompt: "Enter domain username in format dasId@domain.next"
      private: no
    - name : password
      prompt: "Enter the password for the domain user. Please note that the password you enter will not be displayed on the screen.\r\nPassword"
      private: yes
      unsafe: yes

  tasks:
    - name: readSecretVaultEntry - domain admin account
      include_role:
        name: dhc-readSecretVaultEntry
      vars:
        vaultMachineName: "activedirectory"
        vaultAccountName: "administrator"

    - name: Set facts
      set_fact:
        ansible_user: "administrator@{{ searchDomain | upper }}"
        ansible_password: "{{ readPassword[0].value }}"
        ansible_winrm_transport: kerberos
        ansible_connection: winrm
        ansible_winrm_server_cert_validation: ignore
      no_log: true

    - name: Intitating Windows Upgrade from 2016 to 2022.
      include_role:
        name: dhc-upgradeTssTo2022
```

Playbook runs using role **dhc-upgradeTssTo2022**.

### Role description

This role contains the following tasks:

- Apply proxy settings for all users via registry
- Configure proxy settings
- Download ISO image
- Verify ISO image integrity after download
- Display ISO image information
- Rollback registry key value for Internet Options (per user configurations)
- Ensure that ISO image is mounted
- Print message after ISO image is mounted successfully.

### Requirements

- Domain username and password need to be provided to access Vault to get server credentials
- Target Host list needs to be provided while executing the playbook

### Role variables

| Variable | Description/Values   | Source   |
| -------- | -------------------- | -------- |
| username | Domain user          | Playbook |
| password | Domain user password | Playbook |

### Dependencies

This role uses following role

- dhc-readSecretVaultEntry

## Post playbook execution steps

1.Launch Windows **Installer.exe** from the DVD drive path. E:\sources\setupprep.exe (E represents the DVD drive).

![Pic1](images/wiInPlaceUpgradeTSSto2022/Pic1.PNG)

2.Once the installer is launched, click on **Change how setup downloads updates**, then **Not Right Now**, then **Next**.

![Pic2](images/wiInPlaceUpgradeTSSto2022/Pic2.PNG)

3.Enter Product Key - ######### and then **Next**

   ![Pic3](images/wiInPlaceUpgradeTSSto2022/Pic3.PNG)

4.Select **Standard Desktop Version** and then **Next**

   ![Pic4](images/wiInPlaceUpgradeTSSto2022/Pic4.PNG)

5.**Accept** the License and then select **Keep Files, Settings and apps**.

![Pic5](images/wiInPlaceUpgradeTSSto2022/Pic5.PNG)

![Pic6](images/wiInPlaceUpgradeTSSto2022/Pic6.PNG)

6.Server will get upgraded to 2022.

## Post upgrade actions

Post-upgrade checks after upgrading a Windows Server from 2016 to 2022:

- [ ] Ensure the server boots up without any errors
- [ ] Check for any missing drivers or hardware issues in Device Manager
- [ ] Verify network connectivity
- [ ] Ensure IP configuration settings are correct
- [ ] Test DNS resolution and DHCP functionality
- [ ] Verify Active Directory functionality
- [ ] Test user logins and access to network resources

## Roll back actions

In case of failed upgrade or instable system after upgrade, revert to the snapshot taken previously before initiating the upgrade process.

If system is still instable after reverting it from snapshot, restore system from previous day's backup.

[^1]: Minimal hardware requirements are defined on Microsoft's website <https://learn.microsoft.com/en-us/windows-server/get-started/hardware-requirements?tabs=cpu#components>
[^2]: ISO image defined in the defaults/main.yml file and can be found at <https://dhcdownload.s3.eu-west-2.amazonaws.com/binaries/en-us_windows_server_2022.iso>

## Windows Remote Desktop (RDS CALs) for Terminal Server

- Microsoft asks that you promptly activate your Remote Desktop Services client access licenses (RDS CALs) to verify that you are a licensed user eligible to receive customized benefits and services.
- Certain data fields are optional. However, some fields are required, and if you do not provide complete information, Microsoft might be unable to activate your license server and client access licenses.
- From a previous Windows Server version to the next version (for example, from Windows Server 2016 to 2022), you'll be required to reactivate the license service. A reactivation is required so that your upgraded server can manage RDS CALs in the next version of Windows Server.
- Reactivating a license server won't result in the loss of the licenses currently installed on the license server. It may also be necessary to reactivate your license server if your certificate expires or becomes corrupt.

Two steps required to perform:

1. Reactivate a license server on tss001 - choose this option via web page: <https://activate.microsoft.com/> and follow the process to reactivate server (first change to use web browser method in licensing manager properties), check product ID in licensing manager properties
2. Install client access licenses - choose this option in activate.microsoft.com
enterprise  agreement

To find the License Key ID:

- On the license server, open the Remote Desktop Licensing Manager.
- Right-click the license server, and then select Install licenses.
- Select Next on the welcome page.
- Select the program you purchased your RDS CALs from, and then select Next.
- Here the License Key can be visible.

```bash
Company Information: Company: ATOS INTERNATIONAL
Enrollment number: 71701455
Country: France
30 CALS
```

>[!NOTE]
>
>***install only on tss001*** - tss002 should use tss001 as license server.
