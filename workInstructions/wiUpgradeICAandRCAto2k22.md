# Automation to Upgrade Windows ICA and RCA servers to 2022

## Table of Contents

- [Automation to Upgrade Windows ICA and RCA servers to 2022](#automation-to-upgrade-windows-ica-and-rca-servers-to-2022)
  - [Table of Contents](#table-of-contents)
  - [ChangeLog](#changelog)
  - [Introduction](#introduction)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Pre-Requisites](#pre-requisites)
  - [Preparations before adding Template to vCenter](#preparations-before-adding-template-to-vcenter)
  - [Preparations before Upgrade of CA servers](#preparations-before-upgrade-of-ca-servers)
  - [vROPS Maintenance Mode](#vrops-maintenance-mode)
  - [Automation to Deploy the new 2k22 server](#automation-to-deploy-the-new-2k22-server)
  - [Validation of New CA Servers](#validation-of-new-ca-servers)
  - [Rollback for CA servers](#rollback-for-ca-servers)

## ChangeLog

|Date|TOS|Issue|Author|Description|
|-----|-----|-----|-----|-----|
|2024-04-15|DHC 1.8.3|VCS-12531|Divyaprakash J|Initial draft|
|2025-03-26|DHC 1.8.3|VCS-15498|Tomasz Korniluk|Adjusted the Document based on ToS bug|
|2025-05-19|DHC 1.8.3|VCS-15897|Divyaprakash J|Updated docuemnt to Check ICA Configuration|

## Introduction

This document describes step-by-step instructions to upgrade windows ICA and RCA servers to 2022.
The Windows upgrade would be done from DHC1.8.3 Environment. We will not be retrofitting it to environments below 1.8.3.

### Audience

DHC deployment engineers

### Scope

This document's goal is to provide the general procedures for upgrading the ICA and RCA Servers to 2k22.

## Pre-Requisites

The minimum hardware requirements for ICA and RCA are:

1. Processor: 1.4 gigahertz (GHz) x64 processor (2 Ghz or faster is recommended). For our Deployment of CA servers we are uing 2Ghz.
2. Memory: We are using 4GB RAM for our CA servers.
3. Available disk space: 40 GB or greater is recommended.
4. On-premises update management with Unified Update Platform (UUP) requires an additional 10 GB of space per Windows version and processor architecture for each version. For more information, see the UUP considerations section.
5. Network adapter: 100 megabits per second (Mbps) or greater (1 GB is recommended)

### Networking

| Source component |  Destination component| Destination port number| Traffic role |
| ---------- | ----------------------- |--------- |--------- |
| ICA,RCA servers (ica001,rca001)   |    TSS server (tss001)| TCP 445 | Server Message Block (SMB) access to share |
| Ansible node (ans001) | ICA,RCA servers (ica001,rca001)| TCP 5985,5986 | WINRM communication to execute remote tasks inside ICA,RCA servers |

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

## Preparations before Upgrade of CA servers

***Please get the updated code from DHC-1.8.3 branch while performing the activity.***

>[!NOTE]
>
> **As the RCA server is in off state across all environments, please switch it on before doing an upgrade.**

Before we start the fresh deployment of 2k22 server for CA in environment, we need to take care of some of the prerequisites.

1. **Backup**: Check with the Backup team on latest backup, if needed let's have a fresh/latest backup of the server before the activity is initiated.

2. **Template Credentials:** Update the credentials for GlobalImage_w2k22 into HashiCorp Vault, This process will be taken care in playbook **importingTemplateForWindows.yml** . Verfiy the credential is available in Vault under templates.

3. **Take a snapshot of the old VM:** Creating a snapshot allows you to capture the current state of the VM before making any changes. This provides a rollback option in case anything goes wrong during the deployment process.

4. **Export scheduled task and copy exported files:** Export the pertinent scheduled task(s) from the source server. This action captures the task configuration and associated parameters. Following the export process, meticulously transfer the exported task file(s) and any associated script files to a designated server earmarked for continued operation. This meticulous copying ensures the preservation of task integrity and functionality in the subsequent operational environment.

5. **Remove roles from Old CA server ( ADCS )**

6. **Change the name of the existing CA servers:** This ensures that there are no conflicts with the new CA server's name once it's deployed.

7. **Disconnect the Network Interface Card (NIC):** Temporarily disconnecting the NIC prevents the old CA servers from accessing the network during the deployment process. This isolation prevents conflicts and ensures a smooth transition to the new WSUS server.

8. **Put the VM in the off state:** Shutting down the old CA servers prevents any conflicts or issues during the deployment of the new WSUS server. It also ensures that resources are not wasted on running unnecessary services.

The specified playbook automates all steps except the first two. Once initiated, the playbook handles the remaining tasks automatically.

```ansible
ansible-playbook demoteCaServer.yml
```

The following diagram outlines the playbook's flow

![CA](images/UpgradeICAandRCAto2k22/prereq.png)

## vROPS Maintenance Mode

We need to place the VROPS resources for servers that we are upgrading into maintenance mode. This ensures that during the upgrade process, alerts for these servers will not be generated in vROps.

This task is included in the prerequisite playbook to ensure that these resources are placed in maintenance mode before proceeding with any further steps

Sample Task

```yaml

    - name: Prepare variable for group
      set_fact: 
        HOSTS: "ca"
      
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

![CA](images/UpgradeICAandRCAto2k22/VropsMaintenance.png)

Steps :-

- Specify the maintenance action as "stop" to halt the resources.
- Specify the hosts to be placed in maintenance mode.
- Obtain an authentication token for making REST API requests to vROps.
- Retrieve details about vROps resources currently in the "STARTED" state.
- Filter the resource list according to specific criteria.
- Stop monitoring activities.

## Automation to Deploy the new 2k22 server

Login to Ansible core VM and navigate to the location where automation code was cloned (most likely user home directory).

Navigate to **update** directory and execute following command:

```ansible
ansible-playbook upgradeICAandRCAto2k22.yml
```

The following diagram outlines the playbook's flow

![CA](images/UpgradeICAandRCAto2k22/automationCA.png)

Steps covered by automation:

This playbook includes both ICA and RCA server.

- Will obtain domain credentials from the user in order to retrieve certain account passwords from vault.
- Deploy new VM with 2k22 template
- Add server to domain
- Copy unattend.xml template to server
- Copy files from TSS to respective CA servers
- Preinstall for CA servers - This script will set up the CA servers with CApolicy.inf, which contains information about RenewalKeylength, Validity, CRL Period, and other things.
- Install CA roles to new CA server with old server private Key
- Import registry of old server to new CA servers
- Restore CA database
- Update GPO to rename administrator as c-kathos in ICA server
- Postinstall for CA servers - This script will set up the CA servers in terms of configDN, CAURL, AIURL, and so on.
- Add Templates to ICA server
- Configure ICA for Web enrollment
- Import Scheduled task

## Post-Deployment Configuration Verification for ICA Server

After completing the ICA server deployment, ensure that the following configurations are properly set up and functioning as expected:

## Post-Deployment Configuration Verification for ICA Server

After deploying the ICA server, validate the following configurations to ensure secure and reliable access to the Certificate Services web interface. Take corrective actions only if the required settings are missing or misconfigured.

| **Steps**                                                                                                                       | **Picture**                                                             |
| --------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| **1. Verify WinRM SSL Certificate Installation:**<br> - Launch **Manage Computer Certificates**.<br> - Navigate to **Personal > Certificates**.<br> - **If the WinRM SSL Certificate is present**, continue to the next step.<br> - **If missing:**<br>   - Right-click on **Certificates**, select **All Tasks → Request New Certificates**.<br>   - Follow the **Certificate Enrollment Wizard** to enroll for the **WinRM SSL Certificate**. | ![ICA](images/wiOsUpgrade/WSUSCheck_1.png) ![ICA](images/wiOsUpgrade/WSUSCheck_2.png) |
| **2. Confirm IIS HTTPS Binding for ICA (CertSrv):**<br> - Open **IIS Manager**.<br> - Select **Default Web Site**.<br> - Click **Bindings** from the **Actions** pane.<br> - **If an https binding exists** with hostname **`{{ mgmtDns.ica001.name }}.{{ activeDirectory.domainName }}`** and the correct SSL certificate, proceed to Step 4.<br> - **If not configured:**<br>   - Click **Add...**<br>   - Set Type: `https`, IP Address: `All Unassigned`, Hostname: `{{ mgmtDns.ica001.name }}.{{ activeDirectory.domainName }}`<br>   - Select the appropriate SSL certificate and click **OK**. | ![ICA](images/UpgradeICAandRCAto2k22/ICACheck_3.png) ![ICA](images/UpgradeICAandRCAto2k22/ICACheck_4.png) |
| **3. Apply IIS Changes (if necessary):**<br> - If a new binding was added in Step 2, restart IIS from the **Actions** pane to apply the changes.<br> - If the binding already existed, this step can be skipped. | ![ICA](images/UpgradeICAandRCAto2k22/ICACheck_5.png) |
| **4. Validate HTTPS Access to the CertSrv Page:**<br> - Open a web browser (e.g., Microsoft Edge).<br> - Navigate to: `https://{{ mgmtDns.ica001.name }}.{{ activeDirectory.domainName }}/CertSrv`<br> - Log in using domain credentials if prompted.<br> - **If the Certificate Services page loads**, the configuration is complete.<br> - **If the page fails to load:**<br>   - Double-check the IIS binding and SSL certificate.<br>   - Confirm that IIS was restarted properly. | ![ICA](images/UpgradeICAandRCAto2k22/ICACheck_6.png) |

Ensure each step is completed successfully to confirm that the ICA server is configured correctly and that secure access to the Certificate Services interface is fully operational.

## Validation of New CA Servers

Navigate to **update** directory in **ans001** server and execute following command:

```ansible
ansible-playbook validateCAOperations.yml
```

These are the below steps:-

- Check if ADCS service is running

![CA](images/UpgradeICAandRCAto2k22/ADCS.png)

- Check the connection to the private key of a certificate
- Test the connection to Certification Authority

![CA](images/UpgradeICAandRCAto2k22/Privatekey.png)

- Sign a Cert from CA Server

![CA](images/UpgradeICAandRCAto2k22/Signcert.png)

>[!NOTE]
>
> **After all steps are done , RCA server should be shutdown gracefully.**

## Rollback for CA servers

Navigate to **update** directory in **ans001** server and execute following command:

```ansible
ansible-playbook rollBack_2k22_CA.yml
```

Steps covered by automation:

This playbook includes both ICA and RCA server.

- Will obtain domain credentials from the user in order to retrieve certain account passwords from vault.
- Stop ADCS service
- Remove ADCS from CA servers
- Remove from Domain
- Shutdown and Disconnect Nic
- Rename to "servername" + "2k22-old"
- Rename old server to "servername" from "servername+old"
- Connect Nic and Power On the old CA server
- Add old CA servers to domain
- Add roles to Old CA servers
- Start ADCS service

### Case 1: Demote CA Playbook Failure - ICA

If the demote CA playbook fails while removing the ICA from its domain:

**Remove the ICA Server**:

- Ensure the ICA server is removed from its domain if it hasn't been removed already.

**Run the Playbook with Tag**:

- Execute the following command to run the playbook using the specified tag:

   ```ansible
   ansible-playbook rollBack_2k22_CA.yml --tags revert_config_2k16_ICA
   ```

### Case 2: Demote CA Playbook Failure - RCA

If the demote playbook fails while demoting the RCA like while unistalling ADCS role

**Run the playbook with following Tag**:

   ```ansible
   ansible-playbook rollBack_2k22_CA.yml --tags revert_config_2k16_ICA,revert_config_2k16_RCA
   ```

>[!NOTE]
>
> **Debug statements have been strategically inserted within the code to provide explicit guidance in case of failures. These debug checkpoints cover critical processes such as server deployment, disk initialization, domain join, and task scheduling. Each statement aims to enhance troubleshooting efficiency by pinpointing potential issues and guiding corrective actions during deployment phases. According to the message as show by the code, User should take necessary actions.**
