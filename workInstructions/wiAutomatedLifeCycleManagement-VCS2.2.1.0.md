
# Automated process of Lifecycle Management - VCS-2.2.1.0

## Table of Contents
  
## List of Changes

| Date       | Issue    | Author          | TOS  | Description |
| ---------- | -------- | --------------- | ---- | --------------------- |
| 31/12/2025 | VCS-17804 | Mariusz Stanek |      | Initial version |
| 01/04/2026 | VCS-16875 | Adam Wieczorek |      | Added steps for VROPS policies export/import |

## Introduction

This document describes automated process of Lifecycle Management from VCS-2.1.1.0 to VCS-2.2.1.0.

## Scope

Scope of this Work Instruction covers whole process of updating VCS from version 2.1.1.0 to 2.2.1.0 in automated fashion:

- Update of apiUrl.yml file
- Binaries download.
- Aria Suite Lifecycle preparation for Workspace ONE Access patching.
- VMware Identity Manager 3.3.7.0 CSP-102092 patching.
- Aria Suite Lifecycle 8.18.0 Patch 5 installation.
- Aria Operations for Logs upgrade to version 8.18.5.
- Aria Operations upgrade to version 8.18.5.
- Aria Operations for Networks upgrade to version 6.14.1.
- Aria Operations for Logs Agents update.

## Related Documents

| Document |
| -------- |
| none |

## Prerequisites

- It is expected the upgrade is performed by a person(s) with expert knowledge in VMware, Linux and VCS solution. Engineers must have sufficient privileges.
- Image backups are created and available.
- All accounts are valid (not locked due, for example expired password).
- The playbooks mentioned in this work instruction, unless otherwise specified (for example user *next*), are executed by an engineer logged in with their dedicated domain account.

## Release notes and Instructions

1. [CSP-102092 Patch Instructions for VMware Identity Manager 3.3.7 and VMware Aria Suite Lifecycle 8.18.0 Patch 5](https://knowledge.broadcom.com/external/article/412021)
2. [VMware Aria Suite Lifecycle 8.18 Patch 5 Release Notes](https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-suite-lifecycle/8-18/release-notes/vmware-aria-suite-lifecycle-818-patch-5-release-notes.html)
3. [VMware Aria Operations for Logs 8.18.5 Release Notes](https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-operations-for-logs/8-18/vmware-aria-operations-for-logs-8185-release-notes.html)
4. [VMware Aria Operations 8.18.5 Release Notes](https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-operations/8-18/vmware-aria-operations-8185-release-notes.html)
5. [VMware Aria Operations for Networks Release Notes](https://techdocs.broadcom.com/us/en/vmware-cis/aria/aria-operations-for-networks/6-14/vmware-aria-operations-for-networks-release-notes.html)
6. [vIDM 3.3.x vPostgres DB OAuth2RefreshToken table consumes most space on the appliance leading to service outages](https://knowledge.broadcom.com/external/article/322682)
7. [How to Increase vIDM appliance disk space](https://knowledge.broadcom.com/external/article/319356/how-to-increase-vidm-appliance-disk-spac.html)

## Email notification functionality

Automated upgrade playbook can send email notification upon successful or failed upgrade execution. In order to work it requires providing SRS server FQDN and email notification recipients. Prompts to provide these parameters are described in following sections of this document.

Email notification functionality is based on proper `ansible.cfg` file configuration. Example of required parameters:

```bash
[defaults]
callback_whitelist = profile_tasks, mail
callbacks_enabled = default, mail
[callback_mail]
# SMTP server settings
smtphost = gre25srs001.vx5dhc01.next
sender = noreply@atos.net
to = example.email@atos.net
```

Above parameters allow us to automatically send email notification if any playbook execution will be stopped (failed).

![PlaybookNOK](images/wiAutomatedLifeCycleManagementVCS202/playbookNok.png)

When playbook finishes successfully then email notification will be also generated:

![PlaybookOK](images/wiAutomatedLifeCycleManagementVCS202/playbookOk.png)

IMPORTANT NOTE: If you execute playbook with tags then successful completion email will be only related with part executed for particular tags.

### Extra vars

For email notification functionality to work it is necessary to provide an extra variable `mailRecipientsList` during upgrade playbook execution.  
This variable can be provided during playbook execution either directly in command line or as stored in .json file.

Example:

```bash
ansible-playbook playbookName.yml -e "mailRecipientsList=your.mail@domain.com"
```

or save the following content into a .json file which will be provided during execution

```json
{
  "mailRecipientsList": "your.mail@domain.com"
}
```

And then during execution provide extra vars as a path to .json file:

```bash
ansible-playbook playbookName.yml -e "@/path/to/file/vars.json"
```

## DHC Version matrix

DHC Version matrix is automatically updated by python script described in following sections of this document.

## Rollback

Rollback is possible when snapshot is available for particular upgraded element.

## Upgrade Steps

### Aria Operations Policy export

Due to known issues it is highly advised to export all existing active policies prior to Aria Operations upgrade and then re-import them after the upgrade process is completed.  
To export policy follow below steps:

1. Log into Aria Operations
2. Navigate to `Operations` > `Configuration` > `Policy Definition`
3. From the list select policy with `Status` = `Active`
4. From the three dot menu at the top `...` select `Export`
5. Repeat step 4 for all policies with `Status` = `Active`.

After Aria Operations upgrade is finished, policy can be imported back.
Follow the steps above, but in step 4 select `Import` and select the previously exported policy. In the new dialog window, select `Overwrite existing policy` for the conflicts. Repeat for all exported policies.

### Code change and email functionality enablement

In VCS-2.2.1.0 automated upgrade part of steps is done by shell script `lifecycleUpdatePrereq2110.sh` (File is exactly the same as it was used for VCS-2.1.1.0 so it was not required to duplicate a code with `*2210*` in file name). These steps are:

- Backup ansible code and download code provided by TAG in python prompt. Update, deploy, manage, dhc-collections and version-matrix are automatically upgraded with proper code provided by TAG.
- Configure ansible.cfg file to enable automated email notifications.

To prepare environment for code change and ans001 upgrade login to `ans001` on `next` user and execute below commands:

```bash
cd /opt/dhc/update
git pull
git checkout master-2.0
```

Ensure that file `lifecycleUpdatePrereq2110.sh` is present in `/opt/dhc/update` and execute this file:

```bash
bash  ./lifecycleUpdatePrereq2110.sh
```

Python script will ask to to provide some inputs:

```bash
    "Provide domain username: "
    "Provide domain password (input will be hidden): "
    "Provide Git Username (not the email address): "
    "Provide Git Token (input will be hidden): "
    "Provide DHC Git Tag: "
    "Provide email notification recipients. If more then one use comma (,) to separate: "
    "Provide FQDN of SRS server, ie: gre28srs001.vx8dch01.next: "
```

### Upgrade using upgrade automation

Upgrade process can be done in two scenarios described in detail in Section `Automated upgrade scenarios`.

- First scenario: `all-at-once` - run entire upgrade process in one strike (no ansible tags required).
- Second scenario: `step-by-step` - granular divided into controlled stages (ansible tags required).

To run upgrade using upgrade automation it is required navigate to `/opt/dhc/update` on the `ans001` server and launch upgrade playbook with extra vars parameter and proper ansible tags if you want divide upgrade process into controlled steps.

Right after launching playbook it will ask for user credentials (username and password). Once these are provided there will be no more user prompts during playbook execution.  

Upgrade process will take few hours, this can be around 8hrs or more depending on environment performance and health. Due to that fact make sure your SSH connection to `ans001` server will not be terminated. Configure SSH keep alive packets in your tool of choice for SSH connections. Example: in Putty go to `Connection` and set `Seconds between keepalives` to 60 seconds

## Playbooks structure and ansible TAGs explanation

### Overview

This Section explains structure of playbooks included in `lifecycleUpdateMain.yml` and ansible TAGs implemented in each of them.

### Table of playbooks implemented in lifecycleUpdateMain.yml playbook

- [Get user credentials](#get-user-credentials)
- [Update of apiUrl.yml file](#update-of-apiurlyml-file)
- [Execute prep tasks on LCM appliance](#execute-prep-tasks-on-lcm-appliance)
- [Download binaries](#download-binaries)
- [VMware Identity Manager patching](#vmware-identity-manager-patching)
- [Aria Components upgrade](#aria-components-upgrade)
- [Aria Operations for Logs Agents update](#aria-operations-for-logs-agents-update)

### Get user credentials

**Playbook**: `lifecycleUpdateMain.yml`  

#### Playbook summary

Playbook prompts the user for `username` and `password` if not already defined for example in extra vars.

#### Playbook tags

| Tag name | Description |
| ---------- | -------- |
| always | It runs each time |

### Update of apiUrl.yml file

**Playbook**: `lifecycleUpdateMain.yml`

**Main TAG**: `APIURL_UPDATE`

#### Playbook summary

Playbook updates apiUrl.yml file with new API entries required for upgrade process.

#### Playbook tags

| Tag name | Description |
| ---------- | -------- |
| none |  |

### Execute prep tasks on LCM appliance

**Playbook**: `lifecycleUpdateMain.yml`

**Main TAG**: `LCM_PRE_PATCH`

#### Playbook summary

Playbook executes preparation tasks on Aria Suite Lifecycle Manager required for patching.

#### Playbook tags

| Tag name | Description |
| ---------- | -------- |
| none |  |

#### Play-by-play explanation

- Get LCM authentication token

  Hosts: localhost

  Tags: LCM_PRE_PATCH

  Purpose:
  - Gets authentication token from Aria Suite Lifecycle Manager.

- Create LCM appliance snapshot
  
  Hosts: localhost

  Tags: LCM_PRE_PATCH

  Purpose:
  - Creates Aria Suite Lifecycle Manager VM snapshot.

- Set ansible credential variables for LCM
  
  Hosts: lcm001

  Tags: LCM_PRE_PATCH

  Purpose:
  - Collects root credentials for Aria Suite Lifecycle Manager from HashiVault.

- Copy and execute LCM patch prereq script
  
  Hosts: lcm001

  Tags: LCM_PRE_PATCH

  Purpose:
  - Copies and executes script required for Aria Suite Lifecycle Manager patching prerequisite.

### Download binaries

**Playbook**: `lifecycleUpdateBinaries.yml`

**Main TAG**: `BINARIES`

#### Playbook overview

Playbook downloads binaries required for VCS-2.2.1.0 LCM from S3 according to entries from version-matrix and removes other (not listed in version-matrix) from /data/binaries.

#### Playbook tags

| Tag name | Description |
| ---------- | -------- |
| BINARIES | Downloads binaries from S3 |

### VMware Identity Manager patching

**Imported Playbook**: `lifecycleUpdateVidm.yml`

**Main TAG**: `VIDM_ALL`

#### Playbook summary

This playbook automates database cleanup and the patching of VMware Identity Manager.

##### Playbook requirements

Playbook requires product binaries and patch files for Aria components to be downloaded in previous steps.

#### Table of tags

| Tag name | Description |
| ---------- | -------- |
| VIDM_ALL | Executes all tasks in playbook |

#### Detailed Task-by-Task Explanation

- Credentials Gathering Block

  Purpose: Ensure required credentials are available for the upgrade process.

  - Get user credentials

    Tags: always

    Purpose: Prompts for credentials if username or password are not defined.

- Pre-Patching Check Block

  Purpose: Collect environment and health data, and prepare for patching.

  - Get VIDM data

    Tags: VIDM_ALL

    Purpose: Gathers current VIDM environment data.

  - Get VIDM health check

    Tags: VIDM_ALL

    Purpose: Checks the health status of VIDM before patching.

  - Get VIDM version check

    Tags: VIDM_ALL

    Purpose: Verifies the current VIDM version for compatibility.

  - Create transfer host for variable

    Tags: VIDM_ALL

    Purpose: Sets up a host to transfer patch requirement and package variables.

  - LCM authentication

    Tags: VIDM_ALL

    Purpose: Authenticates with Lifecycle Manager for further operations.

  - Get VMware Identity Manager environment details from LCM

    Tags: VIDM_ALL

    Purpose: Retrieves environment details for VIDM from LCM.

  - Trigger VIDM inventory sync

    Tags: VIDM_ALL

    Purpose: Synchronizes the VIDM inventory in LCM.

- Patching Block

  Purpose: Apply the patch to VIDM if required and ensure system readiness.

  - ReadSecretVaultEntry – IDM root account

    Tags: VIDM_ALL

    Purpose: Retrieves root credentials securely from the vault.

  - Register SSH credentials

    Tags: VIDM_ALL

    Purpose: Sets SSH and sudo credentials for Ansible operations.

  - Check partition sizes

    Tags: VIDM_ALL

    Purpose: Verifies /opt/vmware/horizon and /db partitions are at least 20GB.

  - Display partition size report

    Tags: VIDM_ALL

    Purpose: Outputs partition size status and flags if requirements are met.

  - Fail if either partition is less than 20GB

    Tags: VIDM_ALL

    Purpose: Stops the process if disk space is insufficient.

  - Flag that partition sizes are OK

    Tags: VIDM_ALL

    Purpose: Marks partitions as ready if requirements are met.

  - Patch upload and unzip block

    Tags: VIDM_ALL

    Purpose: Handles patch upload, directory preparation, and unzipping.

  - Send notification on patch installation process start

    Tags: VIDM_ALL

    Purpose: Notifies stakeholders that patching has started.

  - Trigger snapshot creation against target host(s)

    Tags: VIDM_ALL

    Purpose: Creates a VM snapshot for rollback capability.

  - Remove patch directory if present (full cleanup)

    Tags: VIDM_ALL

    Purpose: Cleans up any previous patch directories.

  - Ensure patch directory exists

    Tags: VIDM_ALL

    Purpose: Creates the patch directory if missing.

  - Copy patch zip to appliance

    Tags: VIDM_ALL

    Purpose: Transfers the patch file to the target appliance.

  - Derive patch folder name from zip

    Tags: VIDM_ALL

    Purpose: Determines the folder name for the unzipped patch.

  - Unzip patch file

    Tags: VIDM_ALL

    Purpose: Extracts the patch contents.

  - Remove zip file to reclaim space

    Tags: VIDM_ALL

    Purpose: Deletes the zip file after extraction.

  - Place VIDM VM in maintenance mode in Aria Ops

    Tags: VIDM_ALL

    Purpose: Puts VIDM VM in maintenance mode for safe patching.

  - Ensure sufficient disk space for patchDir after unzip

    Tags: VIDM_ALL

    Purpose: Checks and ensures at least 15GB free space post-unzip.

  - Run cleanup if free space < 15GB

    Tags: VIDM_ALL

    Purpose: Attempts cleanup if space is insufficient.

  - Fail if free space < 15GB after cleanup

    Tags: VIDM_ALL

    Purpose: Stops the process if space is still insufficient.

  - Flag free space is OK

    Tags: VIDM_ALL

    Purpose: Marks free space as sufficient.

- Patch installation block

  Tags: VIDM_ALL

  Purpose: Executes the patch installation script.

  - Run patch automation script

    Tags: VIDM_ALL

    Purpose: Runs the patch script.

  - Notify about reboot

    Tags: VIDM_ALL

    Purpose: Informs about system reboot if triggered.

  - Clear host unreachable state

    Tags: VIDM_ALL

    Purpose: Resets host errors post-reboot.

  - Wait for SSH to go down and come back up

    Tags: VIDM_ALL

    Purpose: Waits for system reboot and SSH availability.

- Post-Reboot Configuration Block

  Purpose: Restore services and exit maintenance mode after patching.

  - Reset connection after reboot

    Tags: VIDM_ALL

    Purpose: Resets Ansible connection.

  - Wait until cloud-init target is active

    Tags: VIDM_ALL

    Purpose: Waits for cloud-init to finish.

  - Debug cloud-init state

    Tags: VIDM_ALL

    Purpose: Outputs cloud-init status.

  - ReadSecretVaultEntry – IDM root account

    Tags: VIDM_ALL

    Purpose: Retrieves credentials again for post-reboot tasks.

  - Register SSH credentials

    Tags: VIDM_ALL

    Purpose: Sets SSH and sudo credentials.

  - Enable Getty service

    Tags: VIDM_ALL

    Purpose: Ensures `getty@tty1.service` is enabled and running.

  - Exit VIDM from maintenance mode in Aria Ops

    Tags: VIDM_ALL

   Purpose: Returns VIDM VM to normal operation.

- Post-Patching Check Block

  Purpose: Validate patch success and synchronize environment.

  - Get VIDM data

    Tags: VIDM_ALL

    Purpose: Gathers post-patch VIDM data.

  - Get VIDM health check

    Tags: VIDM_ALL

    Purpose: Checks VIDM health after patching.

  - Get VIDM version check

    Tags: VIDM_ALL

    Purpose: Verifies VIDM version post-patch.

  - Post-patching version validation fail

    Tags: VIDM_ALL

    Purpose: Fails if patch validation does not pass.

  - LCM authentication

    Tags: VIDM_ALL

    Purpose: Authenticates with LCM for post-patch actions.

  - Get VMware Identity Manager environment details from LCM

    Tags: VIDM_ALL

    Purpose: Retrieves updated environment details.

  - Trigger VIDM inventory sync

    Tags: VIDM_ALL

    Purpose: Synchronizes inventory after patching.

  - Send notification on patch installation process end

    Tags: VIDM_ALL

    Purpose: Notifies stakeholders that patching is complete.

- LogInsight Agent Re-Installation Block (if required)

  Purpose: Ensure LogInsight agent is installed and operational.

  - Send notification on LogInsight Agent re-installation process start

    Tags: VIDM_ALL

    Purpose: Notifies stakeholders about LogInsight agent re-installation.

  - ReadSecretVaultEntry – IDM root account

    Tags: VIDM_ALL

    Purpose: Retrieves credentials for agent installation.

  - Register SSH credentials

    Tags: VIDM_ALL

    Purpose: Sets SSH and sudo credentials.

  - Install vRLI agent on VIDM

    Tags: VIDM_ALL

    Purpose: Installs or reinstalls the LogInsight agent.

  - Send notification on LogInsight Agent re-installation process end

    Tags: VIDM_ALL

    Purpose: Notifies stakeholders that agent re-installation is complete.

### Aria Components upgrade

**Imported Playbook**: `lifecycleUpdateAria.yml`

**Main TAG**: `ARIA_ALL`

#### Playbook summary

This playbook automates the upgrade and patching of VMware Aria Suite components (LCM, Operations, Logs, Networks) for VCS-2.2.1.0. Tags are used for selective execution and organization of preparation, upgrade, patching, and post-upgrade activities.

##### Playbook requirements

Playbook requires product binaries and patch files for Aria components to be downloaded in previous steps.

#### Table of tags

| Tag name | Description |
| ---------- | -------- |
| ARIA_ALL | Executes all tasks in playbook |
| LCM_VERSION_CHECK | LCM version check tasks |
| LCM_HEALTH_CHECK | LCM health check tasks |
| LCM_IMAGE_UPLOAD | LCM image upload tasks |
| LCM_PATCH_INSTALL | LCM patch installation task |
| LCM_PSPACK_INSTALL | LCM PSPACK installation tasks |
| OPS_PRESTAGING | Aria Operations prestaging tasks |
| OPS_VERSION_CHECK | Aria Operations version check tasks |
| OPS_HEALTH_CHECK | Aria Operations health check tasks |
| OPS_IMAGE_UPLOAD | Aria Operations image upload tasks |
| OPS_UPGRADE | Aria Operations upgrade tasks |
| VLI_PRESTAGING | Aria Operations for Logs prestaging tasks |
| VLI_VERSION_CHECK | Aria Operations for Logs version check tasks |
| VLI_HEALTH_CHECK | Aria Operations for Logs health check tasks |
| VLI_IMAGE_UPLOAD | Aria Operations for Logs image upload tasks |
| VLI_UPGRADE | Aria Operations for Logs upgrade tasks |
| VNI_PRESTAGING | Aria Operations for Networks prestaging tasks |
| VNI_VERSION_CHECK | Aria Operations for Networks version check tasks |
| VNI_HEALTH_CHECK | Aria Operations for Networks health check tasks |
| VNI_IMAGE_UPLOAD | Aria Operations for Networks image upload tasks |
| VNI_UPGRADE | Aria Operations for Networks upgrade tasks |
| TELEGRAF_UPDATE | Telegraf agent update tasks |
| IMAGE_UPLOAD | General image upload tasks |
| PRESTAGING | General prestaging tasks |

#### Detailed Task-by-Task Explanation

- Aria Components Upgrade Playbook Bootstrap

  Purpose: Initialize facts, authenticate with LCM, and read the current LCM version before any patching or upgrades.

  - Get LCM authentication token

    Tags: always

    Purpose: Obtains an auth token from vRealize/Aria Lifecycle Manager for subsequent API-driven tasks.

  - Get LCM version

    Tags: always, LCM_VERSION_CHECK

    Purpose: Reads the current LCM version and patch level to decide whether patching or PSPACK installation is required.

- LCM System Patch Installation Block

   Purpose: Prepare and install the Aria LCM system patch when lcmPatchRequired is true, including safety snapshots and maintenance mode handling.

  - Send notification on patching process start

    Tags: LCM_PATCH_INSTALL

    Purpose: Emails a start notification that patching has begun on Aria Suite Lifecycle Manager.

  - LCM health check pre patch

    Tags: LCM_HEALTH_CHECK, LCM_PATCH_INSTALL

    Purpose: Verifies LCM health prior to patch application to reduce risk.

  - LCM patch image upload and mapping

    Tags: LCM_IMAGE_UPLOAD, LCM_PATCH_INSTALL

    Purpose: Uploads patch binaries (ariaProduct: "lcm", uploadType: "patchBinaries") and maps them for installation.

  - Create LCM appliance snapshot

    Tags: LCM_PATCH_INSTALL

    Purpose: Creates a VM snapshot to enable rollback if the patch fails.

  - Place LCM VM in maintenance mode in Aria Ops

    Tags: LCM_PATCH_INSTALL

    Purpose: Puts Aria Suite Lifecycle Manager objects in maintenance mode in Aria Operations with a fixed duration to safely perform patching.

  - Install LCM system patch

    Tags: LCM_PATCH_INSTALL

    Purpose: Executes the patch installation workflow for LCM.

  - Cancel stuck patch request (known issue)

    Tags: LCM_PATCH_INSTALL

    Purpose: Detects and cancels a hanging patch request that may occur due to a known issue, ensuring the workflow can complete.

  - Exit LCM from maintenance mode in Aria Ops

    Tags: LCM_PATCH_INSTALL

    Purpose: Returns Aria Suite Lifecycle objects to normal operation after patching.

  - LCM health check post patch

    Tags: LCM_PATCH_INSTALL

    Purpose: Revalidates LCM health following patch installation.

  - Get LCM version details

    Tags: LCM_PATCH_INSTALL

    Purpose: Confirms the new patch level.

  - Check that patch version is correct

    Tags: LCM_PATCH_INSTALL

    Purpose: Asserts that lcmPatchRequired is now false, indicating patch level is at the expected target. Fails if not.

  - Send notification on successful patching

    Tags: LCM_PATCH_INSTALL

    Purpose: Emails completion with the resulting current patch level on Aria Suite Lifecycle Manager.

- LCM PSPACK Installation Block

   Purpose: Prepare and install the LCM PSPACK package when lcmPspackRequired is true, mirroring safety and validation steps.

  - Send notification on PSPACK installation process start

    Tags: LCM_PSPACK_INSTALL

    Purpose: Emails a start notification that PSPACK installation has begun.

  - LCM check

    Tags: LCM_HEALTH_CHECK, LCM_PSPACK_INSTALL

    Purpose: Ensures LCM is healthy before PSPACK installation.

  - LCM prep

    Tags: LCM_IMAGE_UPLOAD, LCM_PSPACK_INSTALL

    Purpose: Prepares LCM by uploading or staging PSPACK images.

  - Create LCM appliance snapshot

    Tags: LCM_PSPACK_INSTALL

    Purpose: Creates a snapshot for rollback safety.

  - Place LCM VM in maintenance mode in Aria Ops

    Tags: LCM_PSPACK_INSTALL

    Purpose: Places Aria Suite Lifecycle Objects in maintenance mode during PSPACK installation.

  - LCM PSPACK installation

    Tags: LCM_PSPACK_INSTALL

    Purpose: Installs the PSPACK on Aria Suite Lifecycle Manager.

  - Exit LCM from maintenance mode in Aria Ops

    Tags: LCM_PSPACK_INSTALL

    Purpose: Returns Aria Suite Lifecycle Manager VM to normal operation.

  - LCM check

    Tags: LCM_PSPACK_INSTALL

    Purpose: Performs post-install health checks.

  - Get LCM version

    Tags: LCM_PSPACK_INSTALL

    Purpose: Reads LCM version to confirm PSPACK application.

  - Check that PSPACK version is correct

    Tags: LCM_PSPACK_INSTALL

    Purpose: Asserts that lcmPspackRequired is now false; fails if version is not at the expected target.

  - Send notification on successful patching

    Tags: LCM_PSPACK_INSTALL

    Purpose: Emails completion including the resulting currentLcmPspack.

- Prestaging Block for Aria Products (After PSPACK)

  Purpose: Validate health and upload product binaries for Log Insight (VLI), Aria Operations (OPS), and Aria Operations for Networks (VNI) to accelerate upgrades. Executed when pspackInstalled is true.

  - VLI check

    Tags: VLI_PRESTAGING, PRESTAGING

    Purpose: Verifies Aria Operations for Logs health before image uploads.

  - OPS check

    Tags: OPS_PRESTAGING, PRESTAGING

    Purpose: Verifies Aria Operations health before image uploads.

  - VNI check

    Tags: VNI_PRESTAGING, PRESTAGING

    Purpose: Verifies Aria Operations for Networks health before image uploads.

  - VLI image upload

    Tags: VLI_PRESTAGING, PRESTAGING, IMAGE_UPLOAD

    Purpose: Uploads product binaries for Aria Operations for Logs to Aria Suite Lifecycle Manager.

  - OPS image upload

    Tags: OPS_PRESTAGING, PRESTAGING, IMAGE_UPLOAD

    Purpose: Uploads product binaries for Aria Operations to Aria Suite Lifecycle Manager.

  - VNI image upload

    Tags: VNI_PRESTAGING, PRESTAGING, IMAGE_UPLOAD

    Purpose: Uploads product binaries for Aria Operations for Networks to Aria Suite Lifecycle Manager

- VLI (Aria Operations for Logs) Upgrade Block

  Purpose: Upgrade VLI when pspackInstalled is true and vrliUpgraded is false, including maintenance handling and validation.

  - VLI Version check

    Tags: always, VLI_VERSION_CHECK

    Purpose: Compares desired version from the version matrix with Aria Suite Lifecycle Manager recorded product version for Aria Operations for Logs.

  - VLI check

    Tags: VLI_HEALTH_CHECK, VLI_UPGRADE

    Purpose: Validates the health state of Aria Operations for Logs before the upgrade.

  - VLI image upload

    Tags: VLI_IMAGE_UPLOAD, VLI_UPGRADE

   Purpose: Uploads Aria Operations for Logs product binaries ahead of the upgrade.

  - Send notification on VLI installation process start

    Tags: VLI_UPGRADE

    Purpose: Emails the start notification for Aria Operations for Logs.

  - Place Aria Operations for Logs VMs in maintenance mode in Aria Ops

    Tags: VLI_UPGRADE

    Purpose: Sets Aria Operations for Logs objects into maintenance mode for the upgrade window.

  - VLI upgrade

    Tags: VLI_UPGRADE

    Purpose: Executes the product upgrade via Aria Suite Lifecycle Manager.

  - Exit Aria Operations for Logs from maintenance mode in Aria Ops

    Tags: VLI_UPGRADE

    Purpose: Returns Aria Operations for Logs to normal monitoring state in Aria Operations.

  - VLI post upgrade check

    Tags: VLI_UPGRADE

    Purpose: Validates Aria Operations for Logs health after upgrade.

  - VLI Version check

    Tags: VLI_UPGRADE

    Purpose: Re-checks the version post-upgrade and sets flag stating if product is upgraded.

  - Check that version is correct

    Tags: VLI_UPGRADE

    Purpose: Asserts vrliUpgraded is true; fails if version is not at the expected target.

  - Send notification on successful upgrade

    Tags: VLI_UPGRADE

    Purpose: Emails completion with resulting current Aria Operations for Logs version.

- OPS (Aria Operations) Upgrade Block

  Purpose: Upgrade Aria Operations when PSPACK is installed and Aria Operations is not upgraded yet. Includes maintenance for both Aria Operations.

  - OPS Version check

    Tags: always, OPS_VERSION_CHECK

    Purpose: Compares desired version with the version recorded in LCM (ariaProductInVersionMatrix: "ops", ariaProductInLcm: "vrops").

  - OPS check

    Tags: OPS_HEALTH_CHECK, OPS_UPGRADE

    Purpose: Validates Aria Operations health components prior to upgrade.

  - OPS image upload

    Tags: OPS_IMAGE_UPLOAD, OPS_UPGRADE

    Purpose: Uploads Aria Operations product binaries to Aria Suite Lifecycle Manager.

  - Send notification on OPS installation process start

    Tags: OPS_UPGRADE

    Purpose: Emails upgrade start for Aria Operations.

  - Place Aria Operations VMs in maintenance mode in Aria Ops

    Tags: OPS_UPGRADE

    Purpose: Places OPS and CPX VMs into maintenance mode for the upgrade window.

  - OPS upgrade

    Tags: OPS_UPGRADE

    Purpose: Executes the Aria Operations upgrade via Aria Suite Lifecycle Manager.

  - Exit Aria Operations from maintenance mode in Aria Ops

    Tags: OPS_UPGRADE

    Purpose: Restores both OPS and CPX VMS to normal operations.

  - Post upgrade OPS check

    Tags: OPS_UPGRADE

    Purpose: Validates Aria Operations health after the upgrade.

  - OPS Version check

    Tags: OPS_UPGRADE

    Purpose: Confirms the upgraded version and sets flag for Aria Operations upgraded state.

  - Check that version is correct

    Tags: OPS_UPGRADE

    Purpose: Asserts Aria Operations is upgraded; fails if version is not at the expected target.

  - Send notification on successful upgrade

   Tags: OPS_UPGRADE

   Purpose: Emails completion with current Aria Operations product version.

  - Update Telegraf agents

    Tags: TELEGRAF_UPDATE

    Purpose: Refreshes Telegraf agents after a successful OPS upgrade to align telemetry with the updated stack. Executed only when Aria Operations is upgraded.

- VNI (Aria Operations for Networks) Upgrade Block

  Purpose: Upgrade Aria Operations for Networks when PSPACK is installed and when Aria Operations for Networks is not upgraded yet. Includes maintenance handling for required VMs.

  - VNI Version check

    Tags: always, VNI_VERSION_CHECK

    Purpose: Compares desired version with the Aria Suite Lifecycle Manager recorded product version for Aria Operations for Networks.

  - VNI check

    Tags: VNI_HEALTH_CHECK, VNI_UPGRADE

    Purpose: Validates Aria Operations for Networks health prior to upgrade.

  - VNI image upload

    Tags: VNI_IMAGE_UPLOAD, VNI_UPGRADE

    Purpose: Uploads Aria Operations for Networks product binaries to Aria Suite Lifecycle Manager.

  - Send notification on VNI installation process start

    Tags: VNI_UPGRADE

    Purpose: Emails upgrade start for Aria Operations for Networks.

  - Place Aria Operations for Networks VMs in maintenance mode in Aria Ops

    Tags: VNI_UPGRADE

    Purpose: Places both VNI and VNC VMs into maintenance mode for safe upgrade.

  - VNI upgrade

    Tags: VNI_UPGRADE

    Purpose: Executes the product upgrade via Aria Suite Lifecycle Manager.

  - Exit Aria Operations for Networks from maintenance mode in Aria Ops

    Tags: VNI_UPGRADE

    Purpose: Restores VNI and VNC VMs to normal operations.

  - VNI post upgrade check

    Tags: VNI_UPGRADE

    Purpose: Validates Aria Operations for Networks health after upgrade.

  - VNI Version check

    Tags: VNI_UPGRADE

    Purpose: Confirms the upgraded version and sets flag for Aria Operations for Networks.

  - Check that version is correct

    Tags: VNI_UPGRADE

    Purpose: Asserts Aria Operations for Networks is upgraded; fails if version is not at the expected target.

  - Send notification on successful upgrade

    Tags: VNI_UPGRADE

    Purpose: Emails completion with current Aria Operations for Networks version.

### Aria Operations for Logs Agents update

**Imported Playbook**: `lifecycleUpdateVrliAgents.yml`

**Main TAG**: `VLI_AGENTS_UPDATE`

#### Playbook summary

This playbook automates the upgrade of Aria Operations for Logs Agents on VMs.

#### Playbook Requirements

none

#### Table of tags

| Tag name | Description |
|----------|-------------|
| VLI_AGENTS_UPDATE | Executes all tasks in playbook |

#### Play-by-play explanation

- Checks if Aria Operations for Logs is already upgraded

  Hosts: localhost

  Tags: VLI_AGENTS_UPDATE

  Purpose:
  - Checks if Aria Operations for Logs is already upgraded

- Update Aria Operations for Logs agents
  
  Hosts: linux, windows, idm, marketplace, ops, vcs, vni, srm, vsr, sddcmanager

  Tags: VLI_AGENTS_UPDATE

  Purpose:
  - Updates Aria Operations for Logs agents.

- Send notification on Aria Operations for Logs Agents update process stop

  Tags: VLI_AGENTS_UPDATE
  
  Purpose: Send notification on Aria Operations for Logs Agents update process stop

## Automated upgrade scenarios

### First scenario: all-at-once

In this scenario all upgrade steps are performed in one go, tags are not being used.
Execution order will be as follows:

1. Get user credentials if not provided as extra vars
2. Update apiUrl.yml file with required API endpoints
3. Install prerequisite script on Aria Lifecycle Manager appliance
4. Download binaries
5. Install Aria Identity Manager (VIDM) patch
6. Install Aria Lifecycle Manager patch
7. Install Aria Lifecycle Manager (if required)
8. Install Aria Operations for Logs update
9. Install Aria Operations update
10. Install Aria Operations for Networks update
11. Update VLI agents on client nodes

#### Before you start

1. Ensure that passwords and certificates are proper in SDDC Manager - upgrade process also checks them but intentionally fails if any of them has some problem.  
2. Check if product synchronization between LCM and upgraded components works.  
3. Make sure there are no open alarms on any of the upgraded products

#### Execution

Run main upgrade playbook:

```bash
ansible-playbook lifecycleUpdateMain.yml -e "@~/vars.json"
```

### Second scenario: step-by-step

In this scenario upgrade process is divided into smaller steps controlled by ansible tags.  
Proposed scenario breaks down upgrade into smaller steps which can be described as preparation tasks and upgrade tasks.  

#### Before you start

As always, make sure environment is in healthy state and recent backups are available.

#### Execution

As mentioned before this scenario consists of two main parts:

1. Preparation part - contains steps that can be executed outside of maintenance window and do not require any downtime
2. Upgrade tasks - contains steps that impact particular product availability and should be performed during maintenance window

##### Preparation part tasks

These tags can be executed together or separately. **When executed separately it is important to maintain order as provided below:**

- APIURL_UPDATE
- BINARIES
- LCM_PATCH_PRESTAGING
- LCM_PSPACK_PRESTAGING
- PRESTAGING

Example:

```bash
ansible-playbook lifecycleUpdateMain.yml -e "@~/vars.json" --tags APIURL_UPDATE,BINARIES,LCM_PATCH_PRESTAGING,LCM_PSPACK_PRESTAGING,PRESTAGING
```

This will do following tasks:

- update apiUrl file
- download binaries from S3 to ans001
- check health of all Aria components - LCM, VLI, OPS, VNI
- upload binaries and perform binary mappings for all products in scope of upgrade

##### Upgrade part tasks

These tags can be executed together or separately. **When executed separately it is important to maintain order as provided below:**

- VIDM_ALL
- LCM_PRE_PATCH
- LCM_PATCH_INSTALL
- LCM_PSPACK_INSTALL
- VLI_UPGRADE
- OPS_UPGRADE
- VNI_UPGRADE
- VLI_AGENTS_UPDATE

> **Important note:** Following components **must** be patched/upgraded in the same maintenance window and in the provided order:
>
> 1. VIDM_ALL
> 2. LCM_PRE_PATCH
> 3. LCM_PATCH_INSTALL

If high control over installation progress and time is required it is best to run these tags separately in provided order.

```bash
ansible-playbook lifecycleUpdateMain.yml -e "@~/vars.json" --tags VIDM_ALL
```

```bash
ansible-playbook lifecycleUpdateMain.yml -e "@~/vars.json" --tags LCM_PRE_PATCH
```

```bash
ansible-playbook lifecycleUpdateMain.yml -e "@~/vars.json" --tags LCM_PATCH_INSTALL
```

```bash
ansible-playbook lifecycleUpdateMain.yml -e "@~/vars.json" --tags LCM_PSPACK_INSTALL
```

```bash
ansible-playbook lifecycleUpdateMain.yml -e "@~/vars.json" --tags VLI_UPGRADE
```

```bash
ansible-playbook lifecycleUpdateMain.yml -e "@~/vars.json" --tags OPS_UPGRADE
```

```bash
ansible-playbook lifecycleUpdateMain.yml -e "@~/vars.json" --tags VNI_UPGRADE
```

```bash
ansible-playbook lifecycleUpdateMain.yml -e "@~/vars.json" --tags VLI_AGENTS_UPDATE
```
