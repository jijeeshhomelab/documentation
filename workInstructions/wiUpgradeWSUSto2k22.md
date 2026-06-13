# Install Windows Server Update Services WSUS Server on Windows Server 2022

## Table of Contents

- [Install Windows Server Update Services WSUS Server on Windows Server 2022](#install-windows-server-update-services-wsus-server-on-windows-server-2022)
  - [Table of Contents](#table-of-contents)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Pre-requisite](#pre-requisite)
  - [Preparations before Deployment of WSUS](#preparations-before-deployment-of-wsus)
  - [Preparations before adding Template to vCenter](#preparations-before-adding-template-to-vcenter)
  - [vROPS Maintenance Mode](#vrops-maintenance-mode)
  - [Automation to Deploy WSUS 2022 server](#automation-to-deploy-wsus-2022-server)
  - [Manual way to Deploy Windows Server Update Services](#manual-way-to-deploy-windows-server-update-services)
  - [Rollback procedures](#rollback-procedures)
  - [Errors](#errors)
  - [Port Details](#port-details)
  - [Potential Issues](#potential-issues)

## Changelog

| Date       | TOS     | Issue   |    Author         |    Description    |
| ---------- | ------- | ------- | ----------------- | ----------------- |
| 03-21-2024 | DHC 1.8.3 |   VCS-11984   | Aroop Sethi              |    Manual and Automation deployment of WSUS |
| 05-06-2024 | DHC 1.8.3 |   VCS-13023   | Aroop Sethi              |    Refining the documentation for WSUS      |
| 02-05-2025 | DHC 1.8.3 |   VCS-14999   | Krishnasai Dandanayak    |    Refining the documentation for WSUS and Potential Issues has been added     |
| 02-07-2025 | DHC 1.8.3 |   VCS-15379   | Krishnasai Dandanayak    |    Updated the Port details to communicate with ICA for certificates     |
| 03-26-2025 | DHC 1.8.3 |   VCS-15498   |Tomasz Korniluk           |    Update with network prerequisites based on ToS Bug story|
| 03-27-2025 | DHC 1.8.3 |   VCS-15535   | Divyaprkash J            |    Update steps to check WSUS Configuration related to Certificate |

## Introduction

This document describes step-by-step instructions to deploy Windows Server Update Services(WSUS) 2k22 Server from scratch.
The Windows upgrade would be done from DHC1.8.3 Environment. We will not be retrofitting it to environments below 1.8.3.

### Audience

DHC deployment engineers

### Scope

The scope of this document is to cover the overall steps to deploy a fresh WSUS 2k22 Server.

## Pre-requisite

The minimum hardware requirements for WSUS are:

 1. Processor: 1.4 gigahertz (GHz) x64 processor (2 Ghz or faster is recommended). For our deployment of WSUS, we allocated 2 Ghz.
 2. Memory: WSUS requires an additional 2 GB of RAM apart from what's required by the server and all other services or software. For our deployment of WSUS, we allocated a total of 8 GB of RAM to accommodate these requirements effectively.
 3. Available disk space: 40 GB or greater is recommended. In our automated deployment setup, we have provisioned 250 GB of additional disk space.
 4. On-premises update management with Unified Update Platform (UUP) requires an additional 10 GB of space per Windows version and processor architecture for each version. For more information, see the UUP considerations section.
 5. Network adapter: 100 megabits per second (Mbps) or greater (1 GB is recommended)

### Network requirements

The mandatory network traffic needs to be allowed before upgrade implementation starts.

| Source component            |  Destination component| Destination port number| Traffic role |
| -------------------- | ----------------------- |--------- |--------- |
|Ansible node (ans001)|WUS (wus001)|TCP 5985,5986|WINRM communication to execute remote tasks inside WUS server|
|WUS server (wus001))|TSS server (tss001)| TCP 445 | Server Message Block (SMB) access to share|
|WUS server (wus001))|ICA,RCA servers (ica001,rca001)|TCP 135| RPC communication for ICA certificates enrollment activities|

>[!NOTE]
> **Verify NSX-T management firewall rulesets to ensure communication is allowed for the above components.**

## Preparations before Deployment of WSUS

***Please get the updated code from DHC-1.8.3 branch while performing the activity.***

Before we start the fresh deployment of WSUS 2k22 server into the environment, we need to take care of some of the prerequisites.

 1. **Backup**: Check with the Backup team on latest backup, if needed let's have a fresh/latest backup of the server before the activity is initiated.

 2. **Template Credentials:** Integrate the credentials for GlobalImage_w2k22 into HashiCorp Vault, This process will be taken care in playbook **importingTemplateForWindows.yml** . Verfiy the credential is available in Vault under templates.

 3. **Take a snapshot of the old WSUS VM:** Creating a snapshot allows you to capture the current state of the VM before making any changes. This provides a rollback option in case anything goes wrong during the deployment process.

 4. **Export scheduled task and copy exported files:** Export the pertinent scheduled task(s) from the source server. This action captures the task configuration and associated parameters. Following the export process, meticulously transfer the exported task file(s) and any associated script files to a designated server earmarked for continued operation. This meticulous copying ensures the preservation of task integrity and functionality in the subsequent operational environment.

 5. **Change the name of the existing WSUS server:** This ensures that there are no conflicts with the new WSUS server's name once it's deployed.

 6. **Disconnect the Network Interface Card (NIC):** Disconnecting the NIC prevents the old WSUS VM from accessing the network during the deployment process. This isolation prevents conflicts and ensures a smooth transition to the new WSUS server.

 7. **Put the VM in the off state:** Shutting down the old WSUS VM prevents any conflicts or issues during the deployment of the new WSUS server. It also ensures that resources are not wasted on running unnecessary services.

The specified playbook automates all steps except the first two. Once initiated, the playbook handles the remaining tasks automatically.

```shell
ansible-playbook demoteWsusServer.yml
```

## Preparations before adding Template to vCenter

These steps will help us retrieve a new template from the S3 bucket with the latest changes. We can then modify it for our upgrade purposes and store it in vCenter. If this step has already been completed in a recent upgrade task, it can be skipped.

In instances where a template with an identical name **GlobalImage_w2k22** already exists within the vCenter inventory, it's imperative to execute the following sequence of actions, leveraging code:

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

**NOTE:** We have included a local version-matrix.json in our automation playbook role(within the "dhc-downloadAndUploadBinaries" role). When invoking this role, it will automatically reference the JSON file located within the files in the role itself. The role is designed to identify and retrieve the latest version of a template from S3 based on the information provided in version-matrix.json. This allows seamless automation of downloading the most current template version from the specified S3 bucket and make this environment independent playbook.

In conjunction with the template deployment process, it's imperative to manage the version matrix stored within the group_vars directory in the local directory of the ansible server. Given the possibility of the virtual machine (VM) accessing outdated global image versions, it's strongly advised to update the global image values within the version matrix to ensure alignment with the desired specifications. This proactive approach mitigates the risk of VMs utilizing obsolete image versions and helps maintain consistency across the infrastructure. And the new build of windows server will be the latest upgraded version. Screenshot attached for reference:

![AD](images/wiOsUpgrade/WSUS111.PNG)

## vROPS Maintenance Mode

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

![AD](images/wiOsUpgrade/VropsMaintenance.png)

Steps :-

- Specify the maintenance action as "stop" to halt the resources.
- Specify the hosts to be placed in maintenance mode.
- Obtain an authentication token for making REST API requests to vROps.
- Retrieve details about vROps resources currently in the "STARTED" state.
- Filter the resource list according to specific criteria.
- Stop monitoring activities.

## Automation to Deploy WSUS 2022 server

Windows Server Update Services (WSUS) enables information technology administrators to deploy the latest Microsoft product updates. WSUS is a Windows Server role that can be installed to manage and distribute updates. A WSUS server can be the update source for other WSUS servers within the organization.

Using the specified playbook, the below steps are automated.

1. Deploy VM, Get Domain Name, IP and Server Name.
2. Add second disk for WSUS repository.
3. Join Windows Server to Domain.
4. Create Local User - c-kathos.
5. Install WSUS feature.
6. Install IIS management tools.
7. WSUS post deployment - fix for W2k22. This is useful for ensuring proper permissions are in place post-deployment, That may require access to specific directories for operations and then revert the changes.
8. Copy Configuration PS Script.
9. Executing the PS script will help certificate to get installed in IIS for WSUS site port 8531.
10. Copies 2 MSI installer file to server and execute it.
11. Change Private Memory Limit for adjusting IIS application pool settings to accommodate specific application requirements or optimize server performance.
12. Copy Installation PS Script.
13. Execute PS Script to Import GPO.
14. Copying the exported file and any associated script files to the newly created server
15. Import the scheduled task

Playbook name:

```shell
ansible-playbook upgradeWsusTo2k22.yml
```

## Post-Deployment Configuration Verification for WSUS Server

After completing the WSUS server deployment, ensure that the following configurations are properly set up and functioning as expected:

| **Steps**                                                                                                                     | **Picture**                                                             |
| ----------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| **1. Verify WinRM SSL Certificate Installation:**<br> - Open **Manage Computer Certificates**.<br> - Under **Personal Certificates**, check if the **WinRM SSL Certificate** is listed. If it's not available, follow these steps:<br>   - Expand **Personal** and right-click on **Certificates**.<br>   - Choose **All Tasks** -> **Request New Certificates**.<br>   - In the **Request Certificates Wizard**, select **WinRM SSL** and click **Enroll**. | ![WSUS](images/wiOsUpgrade/WSUSCheck_1.png) ![WSUS](images/wiOsUpgrade/WSUSCheck_2.png) |
| **2. Check IIS Configuration for WSUS:**<br> - Open **IIS Manager**.<br> - To verify if WSUS is configured correctly, expand all options in the **Connections** pane to locate **WSUS Administration**.<br> - Right-click on **WSUS Administration** and select **Edit Bindings**. | ![WSUS](images/wiOsUpgrade/WSUSCheck_3.png) |
| **3. Configure SSL Binding for WSUS:**<br> - In the **Edit Site Binding** window, select **https** and click **Edit**.<br> | ![WSUS](images/wiOsUpgrade/WSUSCheck_4.png) |
| - Under **SSL Certificate**, select the newly enrolled **WinRM SSL Certificate**, then click **OK** and close the window. | ![WSUS](images/wiOsUpgrade/WSUSCheck_5.png) |
| **4. Restart WSUS Server:**<br> - In the **Actions** menu on the right-hand side of IIS Manager, click **Restart Server** to apply changes. | ![WSUS](images/wiOsUpgrade/WSUSCheck_6.png) |

Ensure that all the steps are followed and the configurations are correctly applied to verify the proper functionality of the WSUS server.

## Establishing SSL Connectivity to the WSUS Server

Upon successful deployment of the new WSUS server, the next step involves establishing connectivity to the server via SSL. Follow these professional steps to ensure a secure connection:

| Steps                                    | Picture                            |
| ---------------------------------------- | ---------------------------------- |
| 1. **Launch WSUS Console:** Open the Windows Server Update Services (WSUS) console on the designated administrative workstation. | ![AD](images/wiOsUpgrade/WSUS00.png) |
| 2. **Connect to Server:** Right-click on the "WSUS" node in the console's navigation pane to access the contextual menu.<br /><br />3. **Select "Connect to Server":** From the contextual menu, choose the "Connect to Server" option to initiate the connection setup process. | ![AD](images/wiOsUpgrade/WSUS01.PNG) |
| 4. **Enter Server Name:** In the provided dialog box, input the fully qualified domain name (FQDN) like GRE82WUS001.NX8DHC01.NEXT <br /><br />5. Check mark the "Use Secure Sockets Layer (SSL) to connect to the upstream server" option is selected to establish a secure connection. | ![AD](images/wiOsUpgrade/WSUS02.PNG) |

Upon successful establishment of the connection, the WSUS server will automatically initiate the following actions:

| Steps                                    | Picture                            |
| ---------------------------------------- | ---------------------------------- |
| 1. **Server Discovery:** It will identify Windows servers within the network environment. | ![AD](images/wiOsUpgrade/WSUS03.png) |
| 2. **Update Synchronization:** The WSUS server will synchronize updates from the upstream source to ensure it has the latest patches and updates available. | ![AD](images/wiOsUpgrade/WSUS04.png) |
| 3. **Targeting Updates:** Updates will be targeted towards the identified Windows servers based on predefined criteria such as update classifications, groups, or organizational units. | ![AD](images/wiOsUpgrade/WSUS05.png) |
| 4. **Validation:** Once the targeted host gets the update, it will show like this. | ![AD](images/wiOsUpgrade/WSUS06.png) |

**NOTE:** The Ansible task named **WSUS post deployment - fix for W2k22** is designed to address connectivity issues encountered by WSUS when connecting to the server via SSL. This task temporarily adjusts the permissions on the C:\Windows\Temp directory to grant the IIS_IUSRS group the necessary rights (Modify and Write). Once the connectivity is successfully established, the permissions are reverted to their original settings. This part of fix is added to the automation code.

Incase the automation fails, you can opt the manual steps as specifed.

## Manual way to Deploy Windows Server Update Services

First, you will need to install the Windows Server Update Services (WSUS) role on your server. Follow the below steps to add the WSUS role:

| Steps                                    | Picture                            |
| ---------------------------------------- | ---------------------------------- |
| 1. Open the **Windows Server Manager**.<br /><br />2. Click on **Add roles and features**.<br /><br />3. Click on the Next button. | ![AD](images/wiOsUpgrade/WSUS0.PNG) |
| 4. Select “Role based of feature-based installation” and click on the Next button. | ![AD](images/wiOsUpgrade/WSUS1.png) |
| 5. Select destination server on which you want to install the WSUS server role, and then click Next.| ![AD](images/wiOsUpgrade/WSUS2.png) |
| 6. On the select server roles page, select Windows Server Update Services and then click Next. | ![AD](images/wiOsUpgrade/WSUS3.png) |
| 7. Under Role Services, select the options highlighted in Screenshot and click Next. | ![AD](images/wiOsUpgrade/WSUS4.png) |
| 8. In the Content location selection page, type a valid location to store the updates. For example, you can create a folder named WSUS at the root of drive D specifically for this purpose, and type D:\WSUS as the valid location. | ![AD](images/wiOsUpgrade/WSUS5.png) |
| 9. The Web Server Role (IIS) page opens. Review the information, and then click Next. In select the role services to install for Web Server (IIS), retain the defaults, and then click Next. | ![AD](images/wiOsUpgrade/WSUS6.png) |
| 10. Click on the install button to start the installation. Once the installation is complete, you should see the following screen.<br /><br />11. Restart system to apply the changes. | ![AD](images/wiOsUpgrade/WSUS7.png) |

After installing WSUS server, you will need to configure it using WSUS Server configuration wizard. Follow the below steps to configure the WSUS server:

| Steps                                    | Picture                            |
| ---------------------------------------- | ---------------------------------- |
| 1. Go to the **Windows Server Manager** tools, Then click on the **WSUS Server Configuration wizard**. Click on the Next button | ![AD](images/wiOsUpgrade/WSUS8.png) |
| 2. Select **Synchronize from Microsoft Update** and click on the Next button. | ![AD](images/wiOsUpgrade/WSUS9.png) |
| 3. In Proxy Server Screen, If you are using any proxy server then define it otherwise leave it blank and click on the Next button | ![AD](images/wiOsUpgrade/WSUS10.png) |
| 4. Select your language and click on the Next button.<br /><br />5. Select any product that you want to update and click on the Next button | ![AD](images/wiOsUpgrade/WSUS11.png) |
| 6. In the classifications selection screen, Define your update classification then click on the Next button | ![AD](images/wiOsUpgrade/WSUS12.png) |
| 7. The Set Sync Schedule page enables you to select whether to perform synchronization manually or automatically.<br /><br />- If you select Synchronize manually, you must start the synchronization process from the WSUS Administration Console.<br />- If you select Synchronize automatically, the WSUS server will synchronize at set intervals. | ![AD](images/wiOsUpgrade/WSUS13.png) |
| 8. Click on the Next button to start synchronization. | ![AD](images/wiOsUpgrade/WSUS14.png) |
| 9. Once Sync is completed Enroll the SSL certificate from ICA server | ![AD](images/wiOsUpgrade/WSUS15.png) |
| 10. Once Enrollment get completed, assign the SSL Certificate to WSUS URL and click ok. | ![AD](images/wiOsUpgrade/WSUS16.png) |

Next, we’ll need to change the Group policy and make it point to top of the new server. To redirect Automatic Updates to a WSUS server, follow these steps:

In Group Policy Object Editor, expand **Computer Configuration\Administrative Templates\Windows Components\Windows Update\Configure Automatic Updates**.

Set the intranet update service for detecting updates box and in the **Set the intranet statistics server box**. With the new server details and port as specified in the screenshot.

![AD](images/wiOsUpgrade/WSUS17.png)

The WSU migration from 2016 to 2022 is now complete. After the updates from the new server 2022 synchronize, we can now decommission the old server.

![AD](images/wiOsUpgrade/WSUS18.png)

## Rollback procedures

Rollback procedures are essential in any deployment plan to mitigate risks and ensure business continuity. If any issues arise during the WSUS deployment, you can follow these rollback steps:

1. **Revert Changes:** Revert the changes made to the existing(old) WSUS server, such as the name change and IP address change, to their original configurations and Power On the server.

2. **Restore Snapshot:** If you have taken a snapshot of the old WSUS VM before making changes, you can revert to that snapshot to restore the VM to its previous state.

3. **Validation:** Once the rollback is complete, bring up and validate the old WSUS service back online to resume its normal operation.

```shell
ansible-playbook rollback_2k22_WSUS.yml
```

**NOTE: Investigate and Troubleshoot:** After the rollback, thoroughly investigate the issues encountered during the deployment process. Identify the root cause of the problems and implement corrective actions to prevent them from occurring again in future Deployment/Upgrade attempts.

## Errors

We encountered proxy-related challenges in our environment, highlighted by the following issues:

1. Configuration of Windows Server Update Services (WSUS) within our VMware vCenter environment resulted in unsuccessful attempts to connect to Microsoft's servers for update downloads.

2. While downloading binaries from S3 for template creation, interruptions and connectivity issues with proxies were observed.

To address these issues promptly, we recommend coordinating with the Network and Environment team to troubleshoot and resolve these connectivity challenges.

**NOTE:** Debug statements have been strategically inserted within the code to provide explicit guidance in case of failures. These debug checkpoints cover critical processes such as server deployment, disk initialization, domain join, and task scheduling. Each statement aims to enhance troubleshooting efficiency by pinpointing potential issues and guiding corrective actions during deployment phases. According to the message as show by the code, User should take necessary actions.

### Port Details

>[!NOTE]
>
> ***For Prod: For NSX-T related configurations, please reach out to the DevSecOps or CNT team for assistance***

- Log into NSX with admin credentials.
- Navigate to **Security** -> **Distributed Firewall**.
- Expand the **WSUS** section and locate the **Wsustoica** details.
- Under the **Services** section, add the **TCP-135** port.

### Potential Issues

After upgrade of WSUS to Windows 2022 , The following issues might occur:

1) Self-update not accessible from clients.
2) WSUS content directory not accessible:

- System.Net.WebException: The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
- DSS Authentication Web Service not working.
- SimpleAuth Web Service not working.
- Issues reported when running WsusUtil.exe checkhealth.

![WSUS](images/UpgradeICAandRCAto2k22/wsus_issues.png)

Resolution:

These errors occur due to the new cipher keys not syncing properly with Windows 2022 servers.

**IMPORTANT NOTE: Please make sure to take the Backup of the GPO before disabling and re-enabling the GPO**

To resolve this, perform a new policy export with updated values:

- Change the policy setting to Disable and save it.
- Re-enable the policy setting. The value field will populate with the correct cipher list.
- Path -  GPO setting in MemberServerBasic-v0007 >  Computer Configuration>Policies>Administrative Templates>Network>SSL Configuration Settings>SSL Cipher Suite Order
- The correct cipher list should start with TLS_AES_256_GCM_SHA384 and contain 31 entries.
- Additionally, running the gpupdate command might also help.

As per the TSS documentation for Windows servers, the keys are compliant with the specified standards.[Windows 2022 cipher list](https://learn.microsoft.com/en-us/windows/win32/secauthn/tls-cipher-suites-in-windows-server-2022)
