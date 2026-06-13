# Automation to Upgrade Windows AD servers to 2022

## Table of Contents

- [Automation to Upgrade Windows AD servers to 2022](#automation-to-upgrade-windows-ad-servers-to-2022)
  - [Table of Contents](#table-of-contents)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Pre-Requisites](#pre-requisites)
    - [Network requirements](#network-requirements)
    - [Security requirements](#security-requirements)
    - [Capacity requirements](#capacity-requirements)
    - [Domain controllers health checks requirements](#domain-controllers-health-checks-requirements)
  - [Switch Maintenance Mode](#switch-maintenance-mode)
  - [Preparations before adding Template to vCenter](#preparations-before-adding-template-to-vcenter)
  - [Preparation before starting the AD activity](#preparation-before-starting-the-ad-activity)
  - [AD security Hardening](#ad-security-hardening)
  - [Automation of Manual Demoting the AD server](#automation-of-manual-demoting-the-ad-server)
  - [Manual Method to Demote the Old server (ETA 90 Minutes)](#manual-method-to-demote-the-old-server-eta-90-minutes)
  - [Automation to Deploy the new 2k22 server (ETA 35 minutes)](#automation-to-deploy-the-new-2k22-server-eta-35-minutes)
    - [vCenter SSO settings updates](#vcenter-sso-settings-updates)
    - [Validation](#validation)
  - [RollBack Procedure](#rollback-procedure)
    - [One old AD still healthy](#one-old-ad-still-healthy)
    - [Rollback Process When Both ADs Are Broken](#rollback-process-when-both-ads-are-broken)
  - [Errors \& Troubleshooting](#errors--troubleshooting)
    - [Local Administrator Password reset](#local-administrator-password-reset)
    - [Hashi Vault using Token](#hashi-vault-using-token)
    - [LDAP SSO in vCenter](#ldap-sso-in-vcenter)
    - [Missing LDAP Certificates on Domain Controller Servers](#missing-ldap-certificates-on-domain-controller-servers)
    - [Replication issues within the Domain Controllers](#replication-issues-within-the-domain-controllers)

## Changelog

|Date|TOS|Issue|Author|Description|
|-----|-----|-----|-----|-----|
|2024-03-20|DHC 1.8.3|VCS-11979|Krishnasai Dandanayak|Initial draft|
|2024-05-24|DHC 1.8.3|VCS-12756|Krishnasai Dandanayak|Adding Automation of Manual steps to Demote, Hashi Vault LDAP details in Pre-requisite, Errors & Troubleshooting|
|2024-06-11|DHC 1.8.3|VCS-13024|Krishnasai Dandanayak|Adding Scheduling Tasks, vRops Agent Telegraf, Template Details|
|2025-02-05|DHC 1.8.3|VCS-14999|Krishnasai Dandanayak| Adjusted the document flow and added the information of template |
|2025-02-24| VCS-14999 | Krishnasai Dandanayak | |Adjusted the Document flow for ADC servers |
|2025-03-04| VCS-15379 | Krishnasai Dandanayak | |Updated the steps to fix the issue of missing ldap certifications |
|2025-03-07| VCS-15379 | Krishnasai Dandanayak | |Updated the steps to fix the Replication issue in between Domain Controllers |
|2025-03-20| DHC 1.8.3 | VCS-15598 |Tomasz Korniluk|Updated validation steps accordingly with ToS bug story |
|2025-04-01| DHC 1.8.3 | VCS-15575 |Tomasz Korniluk|Updated prerequisites chapter accordingly with ToS bug story |

## Introduction

This document describes step-by-step instructions to upgrade windows AD servers to 2022.
The Windows upgrade would be done from DHC1.8.3 Environment. We will not be retrofitting it to environments below 1.8.3.

### Audience

DHC deployment engineers

### Scope

The Scope of this document is to explain the upgrade steps for windows server to 2022 version.

***Decommission the Old Server:*** Transfer FSMO roles to the primary server. Export DHCP settings and leases. Retrieve a list of all running services for reference.
***Deploy and Configure the New Windows Server 2022:*** Install and configure the new server. Promote it to a Domain Controller (DC). Import DHCP settings and migrate FSMO roles. Import Group Policy Objects (GPOs).
***Validate Services on the New Server:*** Verify the proper functionality of all migrated services. Ensure DHCP, FSMO roles, and GPOs are operating correctly. Confirm system stability and performance.

## Pre-Requisites

>[!NOTE]
> **This activity should perform in the non-business hours**

***Please get the updated code from DHC-1.8.3 branch while performing the activity checkout the branch to DHC-1.8.3 from ansible server when clone has taken***

- Export the all Credentials from hashi Vault to Cyberark, In case if any failure with AD servers we can use these credentials to login and roll back.

### Network requirements

The mandatory network traffic needs to be allowed before upgrade implementation starts.

| Source component            |  Destination component| Destination port number| Traffic role |
| -------------------- | ----------------------- |--------- |--------- |
|   Domain controllers (adc001,adc002)      |    ICA,RCA servers (ica001,rca001)| TCP 135 | RPC communication for ICA certificates enrollment activities  |
|   Domain controllers (adc001,adc002)      |    TSS server (tss001)| TCP 445 | SMB traffic to store Windows Backup under TSS share drive  |
|   Ansible node (ans001)      |    Domain controllers (adc001,adc002)| TCP 5985,5986 | WINRM communication to execute remote tasks inside domain controllers |

>[!NOTE]
>
> **Verify NSX-T management firewall rulesets to ensure communication is allowed for the above components.**
>
>[!NOTE]
>
> **Validate connection between desired Domain controllers and ICA server (port 135 TCP)**
> Follow troubleshooting chapter to mitigate vCenter SSO logon issues.
> [Errors Troubleshooting/ldap-sso-in-vcenter](wiUpgradeADCto2k22.md#ldap-sso-in-vcenter)

### Security requirements

- Hashi Vault instance healthy and available inside Management workload domain
- Existing Windows 2016 template password secret (inside HashiVault) needs to be updated with credentials that contains lower and upper letters, number and special character
- Store new Windows 2022 template password inside Hashi vault

### Capacity requirements

- Enough free space on OS local disk drive under 1st Windows Termnial Services server (tss001)
- Enough free space on 2nd local disk drive under 1st Windows Termnial Services server to store Active Directory Machine Windows backup (avarage max. backup size ~300GB)
- Enough free space on the Management workload domain vSphere datastore to store deployed new Windows template

### Domain controllers health checks requirements

>[!NOTE]
> Execute below health checks commands inside each affected domain controller server before upgrade implementation starts.
>
> | Health check role|Health checks commands| Expected results|
> | -----------------| ----------------------- |--------- |
> |Validate replication folder state info|```wmic /namespace:\\root\microsoftdfs path dfsrreplicatedfolderinfo get replicationgroupname,replicatedfoldername,state```| ```SYSVOL Share; 4```|
>|List FSMO roles |```netdom query fsmo```|![FSMO-Check](images/wiAutomationofWindowsAdServerUpgradeto2022/FSMO-check.png)|
>|Replication health status |```repadmin /showrepl```|Results from replication status task checks should be successful|

## Switch Maintenance Mode

We need to place the VROPS resources for servers that we are upgrading into maintenance mode. This ensures that during the upgrade process, alerts for these servers will not be generated in vROps.

This task is included in the prerequisite playbook to ensure that these resources are placed in maintenance mode before proceeding with any further steps

Sample Task

```yaml

    - name: Prepare variable for group
      set_fact: 
        HOSTS: "wus"
      
    - name: Prepare variable for group
      set_fact: 
        maintenanceTarget: "{{ groups[HOSTS] }}"
      when: groups[HOSTS] is defined
      
    - name: Prepare variable for single host
      set_fact:
        maintenanceTarget: "{{ HOSTS }}"
      when: groups[HOSTS] is not defined

    - name: configuring maintenance
      import_role:
        name: dhc-configureVropsMaintenance

```

The process follows the flow illustrated in the diagram provided below

![AD](images/wiAutomationofWindowsAdServerUpgradeto2022/VropsMaintenance.png)

Steps :

- Specify the maintenance action as "stop" to halt the resources.
- Specify the hosts to be placed in maintenance mode.
- Obtain an authentication token for making REST API requests to vROps.
- Retrieve details about vROps resources currently in the "STARTED" state.
- Filter the resource list according to specific criteria.
- Stop monitoring activities.

## Preparations before adding Template to vCenter

>[!NOTE]
>
> **These steps will help us retrieve a new template from the S3 bucket with the latest changes. We can then modify it for our upgrade purposes and store it in vCenter. If this step has already been completed in a recent upgrade task, it can be skipped.**

In instances where a template with an identical name already exists within the vCenter inventory, it's imperative to execute the following sequence of actions, leveraging code:

1. **Establishing Connection with AWS S3:** We need to establish a connection from our automation tool (e.g., Ansible) to AWS S3 in order to retrieve binary files stored in S3 buckets. This setup requires proxy configuration. In case of any errors, please refer to the "Error" column located at the bottom of the document for troubleshooting guidance.

2. **Binaries Retrieval and Manipulation:** Retrieving binary files from AWS S3 and performing necessary actions such as unpacking, verifying integrity, or preparing them for deployment.

3. **Modifying VMX Configuration:** Before deploying the OVF (Open Virtualization Format) into vCenter, OVF that we downloaded from S3 will have the VMX version 20 (Used for the greenfield deployment vCenter 8) which is not compatible with vCenter 7 version (Used in the lower versions of DHC environment) so we need to change the version to VMX-19.

4. **Template Existence check:** Verifying the presence of a specific template (e.g., 'GlobalImage_w2k22') in the vCenter before proceeding with upload of the latest template from S3.

5. **Deploy OVF:** Deploying a virtual machine or appliance using an OVF template we downloaded and specifying the proper template name.

6. **Convert VM to template:** Converting a configured virtual machine into a template format within VMware, allowing for quick and consistent deployment of standardized virtual machine configurations.

7. **Update Credential to Vault:** Customize the template password and updated it to the vault.

Using the specified playbook, the above steps are automated.

```shell
ansible-playbook importingTemplateForWindows.yml
```

The following diagram outlines the playbook's flow

![CA](images/UpgradeICAandRCAto2k22/template.png)

## Preparation before starting the AD activity

- The demote playbook will be starting the windowsbackup of the local server on the tss001 server.
- Check the Space on the local drives (e.g., C: and E:) for the TSS001 server before performing the activity, As the backup need to have a space to store it.
- In Hashi Vault, Need to add the both the IP's of the adc servers where currently only primary adc001 IP is located.

![hashi](images/wiAutomationofWindowsAdServerUpgradeto2022/hashi.png)

```shell
ldap://<ADC001 IP address>, ldap://<ADC002 IP address>
```

## AD security Hardening

In general we are using built-in admin creds for running and configuring the fresh build windows servers. This is because we would require the highest privileges on AD to do its activity.
We may also use other accounts with possible domain account privileges. But it is recommended to go with admin user.

Below are the two scenarios which can be considered:

1. Upgrading Windows server in an environment which is not hardened:
This would be a straight forward way of upgrading the servers. We are using built-in Domain account for running the playbook it should run without any issues.

2. Upgrading Windows server in an environment which is hardened:
In this scenario we have provided the elevated temporary access to svc-{{ locationCode }}-ans01 account as built in administrator user account to perform this activity.

To transfer this FSMO role: A user must be a member of this group:

| Role name            |    User Access          |
| -------------------- | ----------------------- |
| Schema Master        |    Schema Admins    |
| Domain Naming Master |   Enterprise Admins     |
| RID Master           |    Domain Admins        |
| PDC Emulator         |    Domain Admins        |
| Infrastructure Master |   Domain Admins        |

In both the cases we can run the security hardening playbook once the upgrade is completed and check out with a scan if possible.

## Automation of Manual Demoting the AD server

 Using the playbook, the below are automated.
 Execute the following Ansible Playbook for upgrading the both active directory servers. Initially it need to be select as adc002 then after finishing the adc002, Need to perform on adc001.
 Here while we are demoting the server, we used the same password of svc- {{ locationCode }}-ans01 user account.

>[!NOTE]
>
> **Before demoting `adc001`, follow these steps:**
>
> >**1. Change the IP in HashiVault (LDAP Configuration)** to reflect the new setup:
> >
> > ```shell
> >ldap://<ADC002 IP address>, ldap://<ADC001 IP address>
> >```
> >
> > `Once the activity is completed, you can revert this configuration to the default one.`
> >
> >**2. Update vCenter SSO with new certificates** to avoid Active Directory user account login issues:
> >
> >-Open SSH session to vCenter
> >-Run following command - make sure to replace `<locationCode>` and `<domain>` with proper data:
> >
> > ```shell
> >openssl s_client -connect <locationCode>adc002.<domain>:636 -showcerts
> >openssl s_client -connect <locationCode>adc001.<domain>:636 -showcerts
> > ```
> >
> > - Copy first certificate from output (starting from first `BEGIN CERTIFICATE` line in output, ending at first `END CERTIFICATE` line found).
> > - Save copied output as `<locationCode>adc002.crt`.
> > - Copy second certificate from output (starting from first `BEGIN CERTIFICATE` line in output, ending at first `END CERTIFICATE` line found).
> > - Save copied output as `<locationCode>adc001.crt`.
> > - Login to vCenter with `administrator@vsphere.local` user account.
> > - Go to Menu and `Administration` and in `Single Sign On` go to `Configuration`.
> > - In Active directory check whether VCS domain can found
> > - In identity sources click on the domain and then `EDIT`
> > - Provide the `svc-< locationCode >-vcs01` AD service account credentials in `Password` field
> > - In `Certificates` field provide crt files for `adc002` and `adc001`
> >
> >**3.Validate `adc002` domain controller** health using the below checks:
> >
> > - Logon into `adc002` using privilieged account : svc-{{ locationCode }}-ans01
> > - Run as Administrator command prompt console (cmd)
> > - Execute command to get replication folder state info(expected result - SYSVOL Share; 4):
> >
> > ```shell
> >wmic /namespace:\\root\microsoftdfs path dfsrreplicatedfolderinfo get replicationgroupname,replicatedfoldername,state
> >```
> >
> > - Execute command to see if all FSMO roles are available:
> >
> >    ```shell
> >     netdom query fsmo
> >    ```
> >
> > - Execute command to see any unexpected replication errors (all results should be successfull):
> >
> >```shell
> >repadmin /showrepl
> > ```
>
> **Below are the following steps for ADC Upgrade**
>
> - First, fully demote the adc002 server. Check vCenter to confirm that the server name has the suffix "-old" and that it is in a powered-off state.
> - Fully promote the adc002 server and perform the necessary validation steps.
> - Once the promotion and validation of adc002 are completed, proceed to fully demote the adc001 server. Verify in vCenter that the server name has the "-old" suffix and that it is powered off.
> - Fully promote the adc001 server and follow the validation procedures.
>
> **Local Administrator user details after demoting ADC server**
>
> Executing the playbook also creates a new entry in HashiCorp Vault for generating and storing local administrator credentials under the path servers/{{ locationCode }}adc-old. This credentials can be used for logging into 2k16 old adc servers after completely demoted.
> Whenever the old 2016 Windows servers are decommissioned from the environment, the stored credentials in HashiCorp Vault can be safely removed.

 1. Windows Local Backup will be taken care and Snapshot will be taken.
 2. Providing the elevated access to svc-{{ locationCode }}-ans01 user as similar to adminitsrator.
 3. FSMO Roles Transfer.
 4. Exporting Scheduling Tasks and copy exported files.
 5. Demoting the server.
 6. While demoting , we are using the template password of the server for administrator ( templateNameWindows ) . This variable will be found under group_vars/all.
 7. Exporting the DHCP leases.
 8. Remove the DHCP Failover.
 9. Removing of Roles.
 10. Server Disjoining from Domain.
 11. NIC disabling from the VC.
 12. Guest OS Shutdown.
 13. Rename of the server to {{ mgmtDns.adc00*.name}}-old.
  
 **Export scheduled task and copy exported files:** Export the pertinent scheduled task(s) from the source server. This action captures the task configuration and associated parameters. Following the export process, meticulously transfer the exported task file(s) and any associated script files to a designated server earmarked for continued operation. This meticulous copying ensures the preservation of task integrity and functionality in the subsequent operational environment.

Playbook name:

```shell
ansible-playbook demoteAdDomainController.yml
```

## Manual Method to Demote the Old server (ETA 90 Minutes)

We will have to first take backup, demote, poweroff and rename the old server and As per the Security hardening the below process will be done by svc-{{ locationCode }}-ans01 account. So please add the role groups mentioned in the Security Hardening section.

>[!NOTE]
>
> **Before demoting `adc001`, follow these steps:**
>
> >**1. Change the IP in HashiVault (LDAP Configuration)** to reflect the new setup:
> >
> > ```shell
> > ldap://<ADC002 IP address>, ldap://<ADC001 IP address>
> > ```
> >
> > `Once the activity is completed, you can revert this configuration to the default one.`
> >
> >**2. Update vCenter SSO with new certificates** to avoid Active Directory user account login issues:
> >
> >- Open SSH session to vCenter
> >- Run following command - make sure to replace `<locationCode>` and `<domain>` with proper data:
> >
> > ```shell
> > openssl s_client -connect <locationCode>adc002.<domain>:636 -showcerts
> > openssl s_client -connect <locationCode>adc001.<domain>:636 -showcerts
> > ```
> >
> > - Copy first certificate from output (starting from first `BEGIN CERTIFICATE` line in output, ending at first `END CERTIFICATE` line found).
> > - Save copied output as `<locationCode>adc002.crt`.
> > - Copy second certificate from output (starting from first `BEGIN CERTIFICATE` line in output, ending at first `END CERTIFICATE` line found).
> > - Save copied output as `<locationCode>adc001.crt`.
> > - Login to vCenter with `administrator@vsphere.local` user account.
> > - Go to Menu and `Administration` and in `Single Sign On` go to `Configuration`.
> > - In Active directory check whether VCS domain can found
> > - In identity sources click on the domain and then `EDIT`
> > - Provide the `svc-< locationCode >-vcs01` AD service account credentials in `Password` field
> > - In `Certificates` field provide crt files for `adc002` and `adc001`
> > - Once certificate added for `adc001` and `adc002` click `SAVE` to update Identity Source
> >
> >**3.Validate `adc002` domain controller** health using the below checks:
> >
> > - Logon into `adc002` using privilieged account : svc-{{ locationCode }}-ans01
> > - Run as Administrator command prompt console (cmd)
> > - Execute command to get replication folder state info(expected result - SYSVOL Share; 4):
> >
> > ```shell
> > wmic /namespace:\\root\microsoftdfs path dfsrreplicatedfolderinfo get replicationgroupname,replicatedfoldername,state
> > ```
> >
> > - Execute command to see any unexpected replication errors (all results should be successfull):
> >
> >    ```shell
> >     repadmin /showrepl
> >    ```
>
> ***Install Microsoft Windows Backup from the roles and initiate the backup and store this backup in the Secondary Disk of terminal server.***

1. **Check FSMO Roles:**

   - Log in to any ADC server.
   - Open a command prompt with admin privileges.
   - Execute the following command:

   ```shell
   netdom query fsmo
   ```

   - Confirm the FSMO roles

   ![FSMO-Check](images/wiAutomationofWindowsAdServerUpgradeto2022/FSMO-check.png)

2. **Migrate FSMO Roles:**

   - Open PowerShell with admin privileges.
   - Run the following commands:

   ```shell
   ntdsutil
   roles
   connections
   connect to server xxxxxadcxx
   quit
   help
   transfer role-name
   ```

     ![Migrating-FSMO-Roles](images/wiAutomationofWindowsAdServerUpgradeto2022/Migrating-FSMO-Roles.png)

3. **Confirm successful migration of FSMO roles:**

     ![FSMO-confirmation](images/wiAutomationofWindowsAdServerUpgradeto2022/FSMO-confirmation.png)

4. **Export DHCP Leases:**
   - In PowerShell, execute:

   ```shell

   Export-DhcpServer -File \\<tss001_server_name>\C$\temp\DHCPdata.xml -Leases -BackupPath \\<tss001_server_name>\C$\temp\DHCPdata -Force -ComputerName <adc002_fqdn> –Verbose

    #Here replace with the <tss001_server_name> as tss001 server name and <adc002_fqdn> as adc001 server fqdn

   ```

5. **Remove AD DS Role:**

   - Open ***Server Manager.***
   - Click on ***Remove Server roles.***
   - Select ***AD DS.***

     ![Remove-Roles-1](images/wiAutomationofWindowsAdServerUpgradeto2022/Remove-Roles-1.png)

6. **Demote the Domain Controller:**

   - Click on ***Demote*** (since it’s a domain controller).
   - Change the user to domain administrator if needed.
   - Check the box for removal of DNS and Global Catalog.
   - Provide the administrator password.
   - Review and click ***Demote***.
   - After demotion, the server will reboot.

      ![Demote1](images/wiAutomationofWindowsAdServerUpgradeto2022/Demote1.png)

      ![Demote2](images/wiAutomationofWindowsAdServerUpgradeto2022/Demote2.png)

      ![Demote3](images/wiAutomationofWindowsAdServerUpgradeto2022/Demote3.png)

      ![Demote4](images/wiAutomationofWindowsAdServerUpgradeto2022/Demote4.png)

7. **Post-Demotion Steps:**

   - Un-authorize DHCP in Server Manager.
   - Remove AD DS, DNS, and DHCP roles.
   - Remove the server’s IP.
   - Remove the server from the domain and rename it to the old name.

      ![Remove-Roles-2](images/wiAutomationofWindowsAdServerUpgradeto2022/Remove-Roles-2.png)

      ![rename-and-removal-from-domain](images/wiAutomationofWindowsAdServerUpgradeto2022/rename-and-removal-from-domain.png)

8. **Final Steps:**

   - Reboot the server.
   - Disconnect NIC's of the respective VM in vCenter.
   - Power off the server and add notes indicating the upgrade to 2k22.

      ![Poweroff](images/wiAutomationofWindowsAdServerUpgradeto2022/Poweroff.png)

## Automation to Deploy the new 2k22 server (ETA 35 minutes)

 Using the playbook, the below are automated.

Execute the following Ansible Playbook for upgrading the both active directory servers. Initially it need to be select as adc002 then after finishing the adc002, Need to perform on adc001.

Here while we are promoting the server, we used the same password of DSRM passwords of each individual server from the hashi. For ADC001 - its takes DSRM password of Hashi, similary to ADC002 server.

>[!NOTE]
>
> **After demoting `adc001`, follow these steps:**
>
> Step 1.**Change the IP in HashiVault (LDAP Configuration)** to reflect the new setup:
>
> ```shell
> ldap://<ADC002 IP address>, ldap://<ADC001 IP address>
> ```
>
> Step 2.**Once the activity is completed**, you can revert this configuration to the default one.
>

 1. Server Creation with the same name and same IP config.
 2. DHCP NIC and SDDC NIC connection.
 3. Attaching and initializing the Second Disk with Label ‘D’.
 4. Adding the server to Domain.
 5. Installing the AD DS, DNS, DHCP roles and Promotion to DC.
 6. DHCP configuring and Invoking the Failover on DHCP.
 7. FSMO roles migration.
 8. Scheduling Tasks importing.

Playbook name:

```shell
ansible-playbook upgradeADto2k22
```

### vCenter SSO settings updates

>[!NOTE]
>**The following chapter is applicable only in case adc002 was successfuly redployed and promoted.**
>
> To mitigate last upgrades issues the below steps needs to be excuted:
>
> **Update vCenter SSO with new certificates** to avoid Active Directory user account login issues:
> >
> >- Open SSH session to vCenter
> >- Run following command - make sure to replace `<locationCode>` and `<domain>` with proper data:
> >
> > ```shell
> > openssl s_client -connect <locationCode>adc002.<domain>:636 -showcerts
> > openssl s_client -connect <locationCode>adc001.<domain>:636 -showcerts
> > ```
> >
> > - Copy first certificate from output (starting from first `BEGIN CERTIFICATE` line in output, ending at first `END CERTIFICATE` line found).
> > - Save copied output as `<locationCode>adc002.crt`.
> > - Copy second certificate from output (starting from first `BEGIN CERTIFICATE` line in output, ending at first `END CERTIFICATE` line found).
> > - Save copied output as `<locationCode>adc001.crt`.
> > - Login to vCenter with `administrator@vsphere.local` user account.
> > - Go to Menu and `Administration` and in `Single Sign On` go to `Configuration`.
> > - In Active directory check whether VCS domain can found
> > - In identity sources click on the domain and then `EDIT`
> > - Provide the `svc-< locationCode >-vcs01` AD service account credentials in `Password` field
> > - In `Certificates` field provide crt files for `adc002` and `adc001`
> > - Once certificates added for `adc002` and `adc001` click `SAVE` to update Identity Source
> >
> > **More detials described inside chapter:** [Errors Troubleshooting/ldap-sso-in-vcenter](wiUpgradeADCto2k22.md#ldap-sso-in-vcenter)
> >
>[!NOTE]
>**The following steps are mandatory only in case adc002 and adc001 was successfuly redployed and promoted.**
>
> To mitigate last upgrades issues the below steps needs to be executed when entire upgrade process has been completed.
>
> **Update vCenter SSO with new certificates** to avoid Active Directory user account login issues:
>
> >- Open SSH session to vCenter
> >- Run following command - make sure to replace `<locationCode>` and `<domain>` with proper data:
> >
> > ```shell
> > openssl s_client -connect <locationCode>adc001.<domain>:636 -showcerts
> > openssl s_client -connect <locationCode>adc002.<domain>:636 -showcerts
> > ```
> >
> > - Copy for each output certificates (starting from first `BEGIN CERTIFICATE` line in output, ending at first `END CERTIFICATE` line found).
> > - Save copied outputs as `<locationCode>adc001.crt` and `<locationCode>adc002.crt`.
> > - Login to vCenter with `administrator@vsphere.local` user account.
> > - Go to Menu and `Administration` and in `Single Sign On` go to `Configuration`.
> > - In Active directory check whether VCS domain can found
> > - In identity sources click on the domain and then `EDIT`
> > - Provide the `svc-< locationCode >-vcs01` AD service account credentials in `Password` field
> > - In `Certificates` field provide crt files for `adc001` and `adc002`
> > - Once certificates added for `adc002` and `adc001`click `SAVE` to update Identity Source
> >
> > **More detials described inside chapter:** [Errors Troubleshooting/ldap-sso-in-vcenter](wiUpgradeADCto2k22.md#ldap-sso-in-vcenter)

### Validation

***Validations needs to be done after each ADC deploy***

Step 1. Once the Playbook is finished. Login to server and open **Active Directory** and check the **Domain Controller OU**.

Step 2. Here it should show us two Domain Controllers.

Step 3. Check the replication status using the below commands

```shell
repadmin /showrepl
```

```shell
wmic /namespace:\\root\microsoftdfs path dfsrreplicatedfolderinfo get replicationgroupname,replicatedfoldername,state
```

>[!NOTE]
> **Expected result** ``- SYSVOL Share; 4``

Step 4. Check if there are any errors in replication. If the replication is not initiated then use the below command to invoke the instant replication.

```shell
repadmin /syncall
```

Step 5. Open the command prompt with admin privileges and check the below command to re-confirm whether the FSMO roles are migrated.

```shell
netdom query fsmo
```

 ![FSMO](images/wiAutomationofWindowsAdServerUpgradeto2022/FSMO.png)

Step 6. Open the DHCP, check whether the ScopeID is created and wait for 15-20 minutes to failover replication settings.

Step 7. Please find the following way of allocating the FSMO roles on the servers

   |    Role name          |        Server           |
   | --------------------- | ----------------------- |
   | Schema Master         |    {{ mgmtDns.adc002.name }}    |
   | Domain Naming Master  |    {{ mgmtDns.adc002.name }}    |
   | RID Master            |    {{ mgmtDns.adc001.name }}    |
   | PDC Emulator          |    {{ mgmtDns.adc001.name }}    |
   | Infrastructure Master |    {{ mgmtDns.adc001.name }}    |

Step 8. Check the linux servers and appliances whether they are logging in with Domain Credentials.

Step 9. If the DHCP Failover creation is failing then it should be taken care in the Manual way. Either The command can execute from adc002 or Manually can be created from the adc002.

```bash
Add-DhcpServerv4Failover -ComputerName $ComputerName -PartnerServer $PartnerServer -Name $Name -LoadBalancePercent $LoadBalancePercent -MaxClientLeadTime $MaxClientLeadTime -ScopeId $ScopeId -Force -Confirm:$false
#here change the values of computername, partnerserver, Name of Failover, LoadBalancerPercent, MaxClientLeadTime, ScopeID
# This details can be find from group_vars file
```

- Login to adc002 server using the ans01 service account.
- Open DHCP console.
- Expand the DHCP and right click on the IPv4 and Click on the configure the Failover.
- Then check the scope and click Next and provide the name of failover as ***adc001-adc002*** and ***50% Load Balancer*** and ***1hr of time***.

Step 10. Gracefully shutdown ``adc001`` to check if works login using Active Directory user account for vCenter instances.

- Login to adc001 server using the ans01 service account.
- Shutdown ``adc001`` server
- Login to Management and Compute vCenter instances with Active Directory user account
- Validate that authentication works
- Logout from the vCenter instances
- Login to ``hsv001`` server using the ans01 service account.
- Validate if have access into Vault portal
- Logout from the ``hsv001``
- Login to vCenter with Active Directory user account
- Power On ``adc001`` server
- ``Execute once again validation checks from steps 3-8``to ensure that ``adc001`` is healthy

>[!NOTE]
>
> **After the Upgrade of Domain Controller server to 2022, The Domain Functional level is up-to-date.**

![DomainFunctionalLevel](images/wiAutomationofWindowsAdServerUpgradeto2022/DomainFunctionalLevel.png).

>[!NOTE]
>
> **Please find the below link for the reference on the latest Domain Functional Level**

- Link for reference for it [Domain-Functional-Level](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/active-directory-functional-levels#windows-server-2016-functional-levels)

## RollBack Procedure

>[!NOTE]
>
> ***Notify relevant teams about the need for rollback.***

### One old AD still healthy

***Note: If First Upgrade as ADC002 is success and ADC001 is having issues and performing the roll back then the both servers need to be in same version. Example both on either 2k16 or on 2k22.***

 1. Identify the Failed ADC: Confirm that the ADC is indeed failed and not recoverable
 2. Remove the Failed ADC:
      - Log into the healthy AD controller
      - Open Active Directory Users and Computers
      - Navigate to the Domain Controllers organizational unit (OU)
      - Right-click the failed ADC and select Delete.
      - Confirm the deletion. Note: This may require removing the server from the domain using Active Directory Sites and Services if it's still listed  there.
 3. Clean Up Metadata:
      - Open Command Prompt with administrative privileges
      - Run ntdsutil
      - Type metadata cleanup and press Enter
      - Follow the prompts to connect to the server, select the domain, and remove the server metadata
 4. ReDeploy the Failed ADC
 5. Promote the Server to a Domain Controller and replicate with the health adc server.
 6. Verify Replication : Run repadmin /replsummary to check for replication issues.

### Rollback Process When Both ADs Are Broken

  1. Get the necessary credentials from the cyberark for accessing to ESXi Hosts
  2. Restore ADC001 from Microsoft windows Backup from the server
      - Booting from recovery media
      - Selecting the system state backup
      - Initiating the restoration process
  3. Verify ADC001 Functionality
      - Once the restore process is complete, boot the server normally
      - Verify that the domain controller services are running
      - Check Event Viewer for any errors or issues related to Active Directory
      - Ensure that replication is working by checking the AD replication status using repadmin.
  4. Install ADC002 from Scratch.
  5. Promote ADC002 to Domain Controller with the help of ADC001.
  6. Verify Replication : repadmin /replsummary to check the replication health

## Errors & Troubleshooting

### Local Administrator Password reset

Step 1. **Download Windows Server 2016 ISO Image:**

- Use the following link to download the Windows Server 2016 ISO image: [2016 ISO Image](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2016)

Step 2. **Upload and Attach ISO to Server:**

- Upload the ISO image to the datastore.
- Attach the ISO to the server.

Step 3. **Restart the Server and Access Boot Menu:**

- Restart the server.
- Before the server fully boots up, open the web console.
- Continuously press F2 until the boot menu wizard appears (as shown in the image).

![boot](images/wiAutomationofWindowsAdServerUpgradeto2022/boot.png)

Step 4. **Access Command Prompt:**

- When the computer starts, you’ll see the message ***“Press any key to boot from CD/DVD/USB.”***
- Use the key combination Shift+F10 to open the command prompt.

Step 5. **Identify System Drive Letter:**

- Run the following command to find the drive letter assigned to the partition where Windows is installed:

```shell
  wmic logicaldisk get volumename,name
```

Step 6. **Replace Utilman.exe with Cmd.exe:**

- Determine the system drive letter (e.g., C:).

- Execute the following commands:

```shell
   copy C:\windows\system32\utilman.exe C:\windows\system32\utilman.exebak
   copy c:\windows\system32\cmd.exe c:\windows\system32\utilman.exe /y
```

Step 7. **Reboot the Server:**

- Reboot the server using the following command

```shell
    wpeutil reboot
```

Step 8. **Reset Administrator Password:**

- Once the server is up, the command prompt will appear.

- Reset the administrator password using:

```shell
    net user administrator password
```

**Note:** Make sure to stored updated administrator credentials inside respectice secret in HashiVault

Step 9. **Restore Orginial Utilman.exe:**

- Restart your computer and boot from the removable USB flash drive or ISO image again.

- Replace utilman.exe with the original file to close the security hole:

```shell
  copy c:\windows\system32\utilman.exebak c:\windows\system32\utilman.exe /y
```

Step 10. **Final Reboot:**

- Remove the flash drive and reboot the server.

## Hashi Vault using Token

Step 1. **Login to Hashi Vault:**

- Log in to the Hashi Vault server using the provided credentials.

Step 2. **Change Directory to Vault Configuration:**

- Navigate to the directory: ``` shell /etc/opt/vault/ ```.

Step 3. **Retrieve Token:**

- Execute the following commands to obtain the token:

```shell
   dec=`gpg --homedir /etc/opt/vault/unsealKeys --no-random-seed-file -qd /etc/opt/vault/unsealKeys/core.crypt`
   gpg -c --cipher-algo AES256 --batch --passphrase $dec -v /etc/opt/vault/unsealKeys/unsealKeys.json
```

Step 4. **Use the Token:**

- A token will appear on the screen.
- Copy the token.
- Log in to the Hashi Vault GUI using the token authentication method.

Step 5. **LDAP Configuration:**

- Adjust the LDAP configuration according to the LDAP role on the AD server.

## LDAP SSO in vCenter

**Configuring a vCenter Single Sign-On Identity Source using LDAP with SSL (LDAPS):**
    - Link for reference for it [Single sign-on](https://knowledge.broadcom.com/external/article/316596/configuring-a-vcenter-single-signon-iden.html)

- Log in to the vSphere Web Client using an Single Sign On Administrator.
- Under Menu, select Administration > Configuration > Identity Sources.
- Click on domain and click on edit.
- Check the all details and update the current existing password of administrator/service account from hashi vault.
- Run the command to gather the SSL certificate information from any Domain Controller desired:

   ```shell
       openssl s_client -connect domaincontrollerfqdn:636 -showcerts
   ```

- When the openssl connect command completes, the full contents of the SSL certificate are displaye d. The root certificate appears similar to:

```shell
        Certificate chain
        0 s:/CN=domain
        i:/DC=com/DC=domain/CN=BRM-CA
        -----BEGIN CERTIFICATE-----
        MIIFyjCCBLKgAwIBAgIKYURFHAAAAAAABDANBgkqhkiG9w0BAQUFADBCMRMwEQYK
        ..........
        ...snip...
        ..........
        TmqX6OuznopBJKNW5Z5LbHzuUCfY8ryBhYZhHKsf9CmZa12j/ODfznFtAgbPNw==
        -----END CERTIFICATE-----
        1 s:/DC=com/DC=domain/CN=BRM-CA
        i:/CN=BRM-ROOT-CA
        -----BEGIN CERTIFICATE-----
        MIIFkjCCBHqgAwIBAgIKYSn5HgAAAAAAAjANBgkqhkiG9w0BAQUFADAWMRQwEgYD
        ..........
        ...snip...
        ..........
        N4C2CAlLaR3sXlHBRNlfsLO+rZo45hwW8Xw3rLD+ETtgKMmAVUI=
        -----END CERTIFICATE-----
```

- Insert the entire root certificate section of openssl output into a .cer file.

>[!NOTE]
>
> ***Debug statements have been strategically inserted within the code to provide explicit guidance in case of failures. These debug checkpoints cover critical processes such as server deployment, disk initialization, domain join, and task scheduling. Each statement aims to enhance troubleshooting efficiency by pinpointing potential issues and guiding corrective actions during deployment phases. According to the message as show by the code, User should take necessary actions.***

## Missing LDAP Certificates on Domain Controller Servers

**Issue Description:** LDAP certificates are missing on Domain Controller servers, potentially impacting services relying on LDAP authentication
>[!NOTE]
>
> ***For Prod: For NSX-T related configurations, please reach out to the DevSecOps or CNT team for assistance***

- Log into NSX with admin credentials.
- Navigate to **Security** -> **Distributed Firewall**.
- Expand the **AD** section and locate the **Adtoica** details.
- Under the **Services** section, add the **TCP-135** port.

### **Below are the steps for Verification of Port 135 Communication**

- Once the port is added in NSX-T, log into the AD servers.
- Execute the following PowerShell command with administrator privileges:

```powershell
    Test-NetConnection -ComputerName <ICA-server-name/IP> -Port 135
```

- Verify that the output is `True`, indicating successful communication between the AD server and the ICA server.

### **Following are the steps for Certificate Auto-Enrollment Configuration (Perform on Both AD Servers)**

- Log into the ADC server and open the **Local Group Policy Management console**.
- Navigate to **Computer Configuration** -> **Windows Settings** -> **Security Settings** -> **Public Key Policies**.
- Locate **Certificate Services Client - Auto Enrollment**, right-click, and select **Properties**.
- In the **Configuration Model** section, select **Enabled**.
- Check the **Renew certificate** option to allow automatic renewal when needed.
- Click **Apply** to save changes.
- Open **Command Prompt** with administrator privileges.
- Run the following command to update the group policy:

```bash
  gpupdate /force
```

- Open the **Local Certification Management console** (`certlm.msc`) to verify that the missing LDAP certificates have been added.
- Finally, again in the **Configuration Model** section, select back **Not Configured**.

## Replication issues within the Domain Controllers

- If any replication issues in between the Domain Controllers then please refer the below Microsoft vendor document.
[Domain Controller Replication issues](https://learn.microsoft.com/en-us/troubleshoot/windows-server/group-policy/force-authoritative-non-authoritative-synchronization)
