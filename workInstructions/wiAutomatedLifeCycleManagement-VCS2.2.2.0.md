# Automated process of Lifecycle Management - 2.2.2.0

## Table of Contents
  
## List of Changes

| Date       | Issue     | Author          | TOS  | Description     |
| ---------- | --------- | --------------- | ---- | --------------- |
| 27/01/2026 | VCS-17806 | Mariusz Stanek  |      | Initial version |
| 16/03/2026 | VCS-18062 | Lukasz Tomaszewski  |      | Major update - full document |
| 24/03/2026 | VCS-18353 | Lukasz Tomaszewski  |      | Minor update - VI domain update chapter |

## Introduction

This document describes automated process of Lifecycle Management from VCS-2.2.1.0 to VCS-2.2.2.0.

IMPORTANT NOTE: this LCM process will modify NSX DFW ruleset to enable vCenters to communicate with internet proxy, as it is required to download vLCM images and Tanzu Supervisor updates.

## Scope

Scope of this Work Instruction covers:

- process of transition from vSphere Lifecycle Manager (vLCM) baselines to vSphere Lifecycle Manager images in partially automated fashion.
- process of updating VCS from version 2.2.1.0 to 2.2.2.0 in automated fashion.

Upgrade process covers below sections:

- VCF upgrade - management domain
  - SDDC Manager patching to version 5.2.2 (fully automated)
- VCF upgrade - management and workload domains
  - VUM to vLCM transition (partially automated).
- VCF upgrade - management domain
  - NSX-T patching to version 4.2.3.1,
  - vCenter server patching to version 8.0 Update 3h,
  - ESXi hosts patching to version 8.0 Update 3h,
  - Tanzu Supervisor update.
- VCF upgrade - workload domain:
  - NSX-T patching to version 4.2.3.1,
  - vCenter server patching to version 8.0 Update 3h,
  - ESXi hosts patching to version 8.0 Update 3h
  - Tanzu Supervisor update.

## Related Documents

| Document |
| -------- |
| Transition from VUM to vLCM - <https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.2.2/workInstructions/wiVUMtoVLCMTransition.md> |

## Prerequisites

- It is expected the upgrade is performed by a person(s) with expert knowledge in VMware, Linux and VCS solution. Engineers must have sufficient privileges.
- Image backups are created and available.
- All accounts are valid (not locked due, for example expired password).
- The playbooks mentioned in this work instruction, unless otherwise specified (for example user *next*), are executed by an engineer logged in with its dedicated domain account.

## Release notes

1. [VMware Cloud Foundation 5.2.2 Release Notes](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-5-2-and-earlier/5-2/vcf-release-notes/vmware-cloud-foundation-522-release-notes.html)
2. [VMware NSX 4.2.3.1 Release Notes](https://techdocs.broadcom.com/us/en/vmware-cis/nsx/vmware-nsx/4-2/release-notes/vmware-nsx-4231-release-notes.html)
3. [VMware vCenter 8.0 Update 3h Release Notes](https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/8-0/release-notes/vcenter-server-update-and-patch-release-notes/vsphere-vcenter-server-80u3h-release-notes.html)
4. [VMware ESXi 8.0 Update 3h Release Notes](https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/8-0/release-notes/esxi-update-and-patch-release-notes/vsphere-esxi-80u3h-release-notes.html)

## Mandatory Extra Vars

Upgrade playbook requires some extra vars to be provided during playbook execution. Most convenient way of supplying these extra vars is by putting them in `update/roles/dhc-updateVcf/vars/main.yml` file. Below is the list of mandatory and optional extra vars parameters already stored in vars file. Some of them are already populated, but there are two mandatory variables required to modify by engineer before further proceed. These are `vcfBundleDownloadToken` and `mailRecipientsList`.

```yaml
# Mandatory
vcfBundleDownloadToken: "" # mandatory variable that needs to be completed
mailRecipientsList: "" # mandatory variable that needs to be completed
targetPatchSdm: "5.2.2.0-24936865"
targetPatchNsxt: "4.2.3.1.0-24954727"
targetPatchVcenter: "8.0.3.00700-25092719"
targetPatchEsxi: "8.0.3-25067014"
configDepot: true
configToken: true
runCertsCheck: true
runCredsCheck: true
runNsxBackups: true
runPrechecks: true
runSdmBackups: true
runVcsBackups: true

# Optional
depotUser: ""
depotPassword: ""
```

| Variable | Value | Description | Mandatory |
| ---------- | -------- | -------- | -------- |
| vcfBundleDownloadToken | Token | Token required for new way depot connectivity | YES |
| mailRecipientsList | email/email list | Email used for LCM reporting sent to mailbox | YES |
| targetPatchVcenter | version and build | Used to overwrite version-matrix value if neccessary | NO |
| targetPatchNsxt | version and build | Used to overwrite version-matrix value if neccessary | NO |
| targetPatchEsxi | version and build | Used to overwrite version-matrix value if neccessary | NO |
| targetPatchSdm | version and build | Used to overwrite version-matrix value if neccessary | NO |
| runPrechecks | true or false | Used to skip prechecks if neccessary (to skip known problems) | YES |
| runSdmBackups | true or false | Used to skip SDDC Manager backup if neccessary (to skip known problems) | YES |
| runNsxBackups | true or false | Used to skip NSX backup if neccessary (to skip known problems) | YES |
| runVcsBackups | true or false | Used to skip vCenter servers backups if neccessary (to skip known problems) | YES |
| configToken | true or false | Used to skip SDDC Manager download token configuration if neccessary | YES |
| configDepot | true or false | Used to skip SDDC Manager depot configuration if neccessary | YES |
| runCredsCheck | true or false | Used to skip credentials validation in SDDC Manager if neccessary | YES |
| runCertsCheck | true or false | Used to skip certifates validation in SDDC Manager if neccessary | YES |
| depotUser | Username | Email address of depot user | NO |
| depotPassword | Password | Password for depot user | NO |

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

When playbook finishes succesfully then email notification will be also generated:

![PlaybookOK](images/wiAutomatedLifeCycleManagementVCS202/playbookOk.png)

IMPORTANT NOTE: If you execute playbook with tags then successfull completion email will be only related with part executed for particular tags.

## DHC Version matrix

DHC Version matrix is automatically updated by commands described in following sections of this document.

## Rollback

Rollback is partially possible when snapshot is available for particular upgraded element.

## Upgrade Steps

Upgrade process has to be devided into several steps due to the VUM transition dependencies:

- Code change and email functionality enablement
- SDDC upgrade to version 5.2.2.
- VUM to vLCM transition (it's partialy automated process executed against both/all vCenters)

Upgrade process can be continued in two scenarios detaily described in Section `Automated upgrade scenarios`.

- First scenario: `all-at-once` - run entire upgrade process in one strike (no ansible tags required).
- Second scenario: `step-by-step` - granular divided into controlled stages (ansible tags required).

To run upgrade using upgrade automation it is required navigate to `/opt/dhc/update` on the `ans001` server and launch upgrade playbook with proper ansible tags if you want divide upgrade process into controlled steps.

Apart from extra vars provided in the file, right after launching playbook it will ask for user credentials (username and password). Once these are provided there will be no more user prompts during playbook execution.  

Upgrade process will take few hours, this can be around 5hrs or more depending on environment. Due to that fact make sure your SSH connection to `ans001` server will not be terminated. Configure SSH keep alive packets in your tool of choice for SSH connections. Example: in Putty go to `Connection` and set `Seconds between keepalives` to 60 seconds

### Code change and email functionality enablement

To prepare environment for code change and ans001 upgrade login to `ans001` on `next` user and execute below commands:

```bash
cd /opt/dhc
git pull
git checkout master-2.2.2 (provide latest VCS 2.2.2 TAG)
```

To enable email functionality, please execute as user `next`:

```bash
cd /opt/dhc/update
bash  ./lifecycleUpdatePrereqEmail.sh
```

Script will ask to to provide some inputs:

```bash
    "Provide FQDN of SRS server (e.g., gre28srs001.vx8dch01.next): "
    "Provide email notification recipients (comma-separated): "
```

### Upgrade SDDC manager to version 5.2.2

Login as a domain user to `ans001` and execute:

```bash
cd /opt/dhc/update
ansible-playbook lifecycleUpdateMain.yml --tags PATCHING_PREPARE
ansible-playbook lifecycleUpdateMain.yml --tags MGT_DOWNLOAD_BUNDLES
ansible-playbook lifecycleUpdateMain.yml --tags MGT_PRECHECK
ansible-playbook lifecycleUpdateMain.yml --tags MGT_SDM_PATCHING
```

### Transition from VUM to vLCM

This process if partially automated. Please, follow the work instruction: <https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.2.2/workInstructions/wiVUMtoVLCMTransition.md>

### Automated upgrade scenarios

As mentioned earlier in this document, upgrade can be processed as all-at-once or step-by-step scenarios. It is strongly recommended to proceed with step-by-step scenario as it allows for better control.

Scenario all-at-once. Login as a domain user to `ans001` and execute:

```bash
cd /opt/dhc/update
ansible-playbook lifecycleUpdateMain.yml
```

Scenario step-by-step.

Continue with MANAGEMENT domain upgrade. Login as a domain user to `ans001` and execute playbook with desired tags, for example:

```bash
cd /opt/dhc/update
ansible-playbook lifecycleUpdateMain.yml --tags MGT_NSXT_PATCHING
ansible-playbook lifecycleUpdateMain.yml --tags MGT_VCS_PATCHING
ansible-playbook lifecycleUpdateMain.yml --tags MGT_ESX_PATCHING
ansible-playbook lifecycleUpdateMain.yml --tags MGT_WITNESS_UPGRADE #(optional for streched clusters)
```

Continue with VI domain upgrade. Login as a domain user to `ans001` and execute playbook with desired tags, for example:

```bash
ansible-playbook lifecycleUpdateMain.yml --tags VI_DOWNLOAD_BUNDLES
ansible-playbook lifecycleUpdateMain.yml --tags VI_PRECHECK
ansible-playbook lifecycleUpdateMain.yml --tags VI_NSXT_PATCHING
ansible-playbook lifecycleUpdateMain.yml --tags VI_VCS_PATCHING
ansible-playbook lifecycleUpdateMain.yml --tags VI_ESX_PATCHING
ansible-playbook lifecycleUpdateMain.yml --tags VI_WITNESS_UPGRADE #(optional for streched clusters)
```

In case Tanzu Supervisor is enabled, please execute:

```bash
ansible-playbook lifecycleUpdateMain.yml --tags MGMT_SUPERVISOR_UPGRADE
ansible-playbook lifecycleUpdateMain.yml --tags VI_SUPERVISOR_UPGRADE
```

## Playbooks structure and ansible TAGs explanation

### Overview

This Section explains structure of playbooks included in `lifecycleUpdateMain.yml` and ansible TAGs implemented in each of them.

### Table of playbooks implemented in lifecycleUpdateMain.yml playbook

- [Get user credentials](#get-user-credentials)
- [Update of apiUrl.yml file and NSX DFW ruleset](#update-of-apiurlyml-file-and-nsx-dfw-ruleset)
- [VCF upgrade - Management domain patching](#vcf-upgrade---management-domain-patching)
- [VCF upgrade - Management domain witness host patching](#vcf-upgrade---management-domain-witness-host-patching)
- [VCF upgrade - Workload domain patching](#vcf-upgrade---workload-domain-patching)
- [VCF upgrade - Workload domain witness host patching](#vcf-upgrade---workload-domain-witness-host-patching)
- [VCF upgrade - Supervisors patching](#vcf-upgrade---supervisors-patching)

### Get user credentials

**Playbook**: `lifecycleUpdateMain.yml`  

#### Playbook overview

Playbook prompts the user for `username` and `password` if not already defined for example in extra vars.

#### Playbook tags

| Tag name | Description |
| ---------- | -------- |
| always | It runs each time |

### Update of apiUrl.yml file and NSX DFW ruleset

**Playbook**: `lifecycleUpdateMain.yml`

**Main TAG**: `PATCHING_PREPARE`

**Task TAGs**: `APIURL_UPDATE`, `DFW_UPDATE`

#### Playbook overview

Playbook updates apiUrl.yml file with new API entries and NSX DFW ruleset (enable vCenters to communicate with proxy) required for upgrade process.

#### Playbook tags

| Tag name | Description |
| ---------- | -------- |
| none | |

### VCF upgrade - Management domain patching

**Imported Playbook**: `lifecycleUpdateMgmtDomain.yml`

**Main TAG**: `MGT_PATCHING`

#### Playbook summary

This playbook automates full lifecycle operations of the Management Domain. It handles SDM (SDDC Manager), NSX‑T Managers, and vCenter patching. Tasks include token configuration, depot validation, domain discovery, prechecks, bundle preparation, snapshots/backups, patch execution, post‑upgrade verification, and Aria Operations maintenance windows.

The playbook is structured around conditional execution based on patch requirements (sdmPatchingRequired, nsxPatchingRequired, vcsPatchingRequired) and uses dedicated TAG groups for selective or step‑by‑step upgrade execution.

#### Playbook Requirements

- Valid VCF Bundle Download Token.
- (Optionally) Depot credentials (depotUser, depotPassword) when SDM depot is not pre‑configured.
- Domain credentials (username, password).
- Working API connectivity to SDDC Manager, NSX Managers, and vCenters.
- Aria Ops reachable for maintenance mode operations.

#### Table of tags

| Tag name | Description |
| ---------- | ----------- |
| MGT_PATCHING | Main driver for Management Domain LCM operations |
| MGT_PRECHECK | Prechecks: running tasks, creds check, certs check, domain validations |
| MGT_DOWNLOAD_BUNDLES | Bundle discovery and download tasks |
| MGT_SDM_PATCHING | SDDC Manager patch flow |
| MGT_NSXT_PATCHING | NSX‑T Manager patch flow |
| MGT_VCS_PATCHING | vCenter patch flow |
| MGT_ESX_PATCHING | ESXi patch flow |
| MGT_CONFIGURE_DOWNLOAD_TOKEN | Token configuration on SDM |
| always | Always executed (credential collection) |

#### Play-by-play explanation

- Set credentials for SDM appliance
  Hosts: localhost
  Tags: always

  Purpose:
  - Ensures domain credentials (username, password) are collected using atos.dhc.askPassWrapper if not already defined.

- Configure download token on SDM
  Hosts: sdm001
  Tags: MGT_PATCHING, MGT_DOWNLOAD_BUNDLES, MGT_PRECHECK, MGT_SDM_PATCHING, MGT_NSXT_PATCHING, MGT_VCS_PATCHING

  Purpose:
  - Sets SDM connection variables through atos.dhc.connectionWrapper (vaultAccountName=vcf).
  - Retrieves SDM API authentication (sdmGetAuth.yml).
  - Configures the VCF download token (sdmConfigureDownloadToken.yml) when configToken=true.

- Upgrade Playbook for VCF components
  Hosts: localhost
  Tags: MGT_PATCHING
  
  - Get SDM API authentication
    Tags: MGT_DOWNLOAD_BUNDLES, MGT_PRECHECK, MGT_SDM_PATCHING, MGT_NSXT_PATCHING, MGT_VCS_PATCHING

    Purpose:
    - Ensures SDM auth token/session is valid for all following tasks.

  - Refresh SDM Management domain details
    Tags: MGT_DOWNLOAD_BUNDLES, MGT_PRECHECK, MGT_SDM_PATCHING, MGT_NSXT_PATCHING, MGT_VCS_PATCHING

    Purpose:
    - Fetches details of MANAGEMENT domain (sdmGetDomains.yml, queryDomainType="MANAGEMENT").
    - Used to extract sdmDomainId, cluster info, etc.

  - Check Depot Status and reconfigure if needed
    Tags: MGT_DOWNLOAD_BUNDLES, MGT_PRECHECK, MGT_SDM_PATCHING, MGT_NSXT_PATCHING, MGT_VCS_PATCHING, MGT_ESX_PATCHING
    Condition: when: configDepot == true

    Purpose:
    - Validates SDM depot configuration and (optionally) repairs it.
    - Uses sdmDepotConf.yml with depotBeforeCheck=true.

  - Check if upgrade and/or patching required
    Tags: always

    Purpose:
    - Determines which products require patching:
      - sdmPatchingRequired
      - nsxPatchingRequired
      - vcsPatchingRequired
      - esxiPatchingRequired

  - Download bundles for patching
    Tags: MGT_DOWNLOAD_BUNDLES, MGT_PRECHECK, MGT_SDM_PATCHING, MGT_NSXT_PATCHING, MGT_VCS_PATCHING, MGT_ESX_PATCHING
    Condition: filteredPatches | length > 0 AND any patching flag is true

    Purpose:
    - Downloads appropriate bundle(s) for MANAGEMENT domain.
    - Uses role: downloadBundlesPreUpgr.yml.

  - Check SDM for running tasks
    Tags: MGT_PRECHECK, MGT_SDM_PATCHING, MGT_NSXT_PATCHING, MGT_VCS_PATCHING

    Purpose:
    - Prevents patch start if SDM is processing another workflow.

  - Check all credentials in SDM
    Tags: MGT_PRECHECK, MGT_SDM_PATCHING, MGT_NSXT_PATCHING, MGT_VCS_PATCHING
    Condition: runCredsCheck == true

    Purpose:
    - Runs sdmCredentialsCheck.yml to validate all stored credentials.

  - Check all certificates in SDM
    Tags: MGT_PRECHECK, MGT_SDM_PATCHING, MGT_NSXT_PATCHING, MGT_VCS_PATCHING
    Condition: runCertsCheck == true

    Purpose:
    - Runs sdmCertificatesCheck.yml for domain certificates using sdmDomainId.

  - Run prechecks before patching
    Tags: MGT_PRECHECK, MGT_SDM_PATCHING, MGT_NSXT_PATCHING, MGT_VCS_PATCHING
    Condition: runPrechecks == true AND (any patch flag == true)

    Purpose:
    - Executes SDM domain checksets (checkType=UPGRADE) to validate readiness.

  - SDDC Manager Patching Block
    Tags: MGT_SDM_PATCHING
    Condition: Executed only when sdmPatchingRequired == true

    - Send notification on patching process start

      Purpose:
      - Informs recipients that SDDC Manager patching has begun.

    - SDDC Manager backup and snapshot
      Condition: runSdmBackups == true

      Purpose:
      - Performs snapshot/backup of SDDC Manager VM (sdm001).

    - Place SDDC Manager components in maintenance mode (Aria Ops)

      Purpose:
      - Pauses monitoring of SDDC Manager components for 180 minutes.

    - Patch SDDC Manager in MANAGEMENT domain

      Purpose:
      - Applies patch type SDDC_MANAGER using patchSdmDomain.yml.

    - Exit SDDC Manager from maintenance mode

      Purpose:
      - Re‑enables monitoring.

  - Reconfigure download token and depot after SDDC manager upgrade
    Tags: MGT_PATCHING, MGT_SDM_PATCHING
    Condition: Executed only when sdmPatchingRequired == true
  
    - Configure VCF download token after upgrade

      Purpose:
      - Re‑configures download token in case upgrade reset the config.

    - Remove depot configuration (post-upgrade)

      Purpose:
      - Clears SDM depot caching/config that may break after patching.

  - Upgrade Playbook for VCF components
    Tags: MGT_PATCHING, MGT_SDM_PATCHING
    Condition: Executed only when sdmPatchingRequired == true

    - Check Depot Status and reconfigure if needed

      Purpose:
      - Re-validates depot with depotBeforeCheck=false.

    - Check if upgrade and/or patching required

      Purpose:
      - Re-checks upgrade requirements.
      - Asserts no further SDM patching is required.

    - Check that patch version is correct
  
      Purpose:
      - Notification if patching not required

  - Post upgrade tasks for SDDC Manager patching
    Tags: MGT_SDM_PATCHING
    Condition: Executed only when vcfPatchingTaskFinished == true

    Purpose:
    - Notification of success
    - Reset vcfPatchingTaskFinished

  - Apply configuration drift
    Tags: MGT_SDM_PATCHING
  
    Purpose:
    - Apply configuration drift.

  - NSX‑T Patching Block
    Tags: MGT_NSXT_PATCHING
    Condition: Executed when nsxPatchingRequired == true

    - Send notification on patching process start

      Purpose:
      - Informs recipients that NSX patching has begun.

    - Gather NSX Managers IPs and Credentials

      Purpose:
      - Collects NSX manager endpoints and admin passwords for next tasks.

    - Check NSX for Open Alarms

      Purpose:
      - Ensures cluster is stable before upgrade.

    - Run NSX configuration backup task
      Condition: runNsxBackups == true

      Purpose:
      - Performs configuration backup per NSX Manager.

    - Place NSX components in maintenance mode (Aria Ops)

      Purpose:
      - Places all NSX components under maintenance window.

    - Patch NSX in MANAGEMENT domain

      Purpose:
      - Applies patch type NSX_T_MANAGER.

    - Exit NSX maintenance mode

      Purpose:
      - Places all NSX components under normal monitoring state in Aria Operations.
  
    - Validate NSX patch outcome

      Purpose:
      - Re-evaluate nsxPatchingRequired.
      - Hard‑fail if NSX still needs patching.

    - Post-upgrade tasks

      Purpose:
      - Success email sent.
      - Reset vcfPatchingTaskFinished.

  - vCenter Patching Block
    Tags: MGT_VCS_PATCHING
    Condition: Executed when vcsPatchingRequired == true

    - Send notification on patching process start

      Purpose:
      - Informs recipients that vCenter patching has begun.

    - Read all VMs from VCS001

      Purpose:
      - Needed to identify vcs001 and vcs002 for snapshots.

    - Place both vCenters in maintenance mode (Aria Ops)

      Purpose:
      - Places all vcs00 components under maintenance window.

    - Snapshot vCenter vcs002 (powered off)
      Condition: runVcsBackups == true

      Purpose:
      - Runs VAMI backup and powered off snapshot of vcs002 .

    - vCenter vcs002 health check post snapshot
      Condition: runVcsBackups == true

      Purpose:
      - Runs vcs002 post reboot healthchecks.

    - Snapshot vCenter vcs001 (powered off)
      Condition: runVcsBackups == true

      Purpose:
      - Runs VAMI backup and powered off snapshot of vcs002 .

    - vCenter vcs001 health check post snapshot
      Condition: runVcsBackups == true

      Purpose:
      - Runs vcs001 post reboot healthchecks.

    - Patch vCenter for MANAGEMENT domain

      Purpose:
      - Applies patch type VCENTER.

    - Exit NSX maintenance mode

      Purpose:
      - Places all vcs00 components under normal monitoring state in Aria Operations.

    - Validate vCenter patch outcome

      Purpose:
      - Re-evaluate vcsPatchingRequired.
      - Fail if patching still required.

    - Post-upgrade tasks

      Purpose:
      - Success email.
      - Reset vcfPatchingTaskFinished.

  - ESX patching block
    Tags: MGT_ESX_PATCHING
    Condition: Executed when esxPatchingRequired == true

    - Place ESXi and Avamar/Networker appliances in maintenance mode in Aria Ops

      Purpose:
      - Places all mgt00, avp00 and nvp00 components under maintenance window.

    - Check and shutdown avamar and networker vms

      Purpose:
      - Shuts down backup vms (avamar or networker).

    - Create cluster image and import it to SDDC Manager

      Purpose:
      - Creates host image used for patching.

    - Patch ESXi hosts in MANAGEMENT domain

      Purpose:
      - Executes ESXi hosts patching in "MANAGEMENT" domain.

    - Check and power on avamar and networker vms

      Purpose:
      - Powers on backup vms (avamar or networker).

    - Exit ESXi/Backup VMs maintenance mode

      Purpose:
      - Places ESXi and Avamar/Networker appliances from maintenance mode in Aria Operations.

  - Post-upgrade tasks
    Tags: MGT_ESX_PATCHING

    Purpose:
    - Success email.
    - Reset vcfPatchingTaskFinished.

### VCF upgrade - Management domain witness host patching

**Imported Playbook**: `lifecycleUpdateVsanWitness.yml`

**Main TAG**: `MGT_WITNESS_UPGRADE`

#### Playbook summary

This playbook automates full lifecycle operations of the Management Domain Witness Host. It handles Witness ESXi host patching. Tasks include SDM authentication, prechecks, patch execution and post‑upgrade cleanup.

#### Playbook Requirements

- Domain credentials (username, password).
- Working API connectivity to SDDC Manager and vCenters.

#### Table of tags

| Tag name | Description |
| ---------- | ----------- |
| MGT_WITNESS_UPGRADE | Management Domain vSAN witness host patch flow |
| always | Always executed (credential collection) |

#### Play-by-play explanation

- Get SDM API authentication
  Hosts: localhost

  Purpose:
  - Retrieves SDM API authentication (sdmGetAuth.yml).

- Get session for VCS authentication
  Hosts: localhost

  Purpose:
  - Retrieves vCenter session authentication (vcsGetSession.yml).

- Get SDM domain details.
  Hosts: localhost

  Purpose:
  - Retrieves information about SDM domain lists required by next task.

- Check cluster type
  Hosts: localhost

  Purpose:
  - Retrieves information about cluster type (isStretched).

- Check if Witness needs upgrade
  Hosts: localhost

  Purpose:
  - Makes a decision if witness patching is required.

- Run prerequisites for vSAN Witness upgrade
  Hosts: localhost

  Purpose:
  - Enables proxy on vCenter.
  - Prepares online depots.
  - Syncs vCenter LCM depots and gets image required for update.
  - Prepares offline bundle for ESXi host.

- Upgrade Witness Appliance
  Hosts: localhost

  Purpose:
  - Upgrades witness appliance.

- Delete Image cluster created for upgrade
  Hosts: localhost

  Purpose:
  - Cleans up requirements created on vCenter (temporary cluster required for image creation).

- Remove Proxy on VCS
  Hosts: localhost

  Purpose:
  - Disables proxy on vCenter.

### VCF upgrade - Workload domain patching

**Imported Playbook**: `lifecycleUpdateViDomain.yml`

**Main TAG**: `VI_PATCHING`

#### Playbook summary

This playbook fully automates lifecycle operations of the Workload Domain. It handles NSX‑T Managers, vCenter and ESXi patching. Tasks include token configuration, depot validation, domain discovery, prechecks, bundle preparation, snapshots/backups, patch execution, post‑upgrade verification, and Aria Operations maintenance windows.

The playbook is structured around conditional execution based on patch requirements (nsxPatchingRequired, vcsPatchingRequired, esxPatchingRequired) and uses dedicated TAG groups for selective or step‑by‑step upgrade execution.

#### Playbook Requirements

- Valid VCF Bundle Download Token.
- (Optionally) Depot credentials (depotUser, depotPassword) when SDM depot is not pre‑configured.
- Domain credentials (username, password).
- Working API connectivity to SDDC Manager, NSX Managers, and vCenters.
- Aria Ops reachable for maintenance mode operations.

#### Table of tags

| Tag name | Description |
| ---------- | ----------- |
| VI_PATCHING | Main driver for Workload Domain LCM operations |
| VI_PRECHECK | Prechecks: running tasks, creds check, certs check, domain validations |
| VI_DOWNLOAD_BUNDLES | Bundle discovery and download tasks |
| VI_NSXT_PATCHING | NSX‑T Manager patch flow |
| VI_VCS_PATCHING | vCenter patch flow |
| VI_ESX_PATCHING | ESXi patch flow |
| VI_CONFIGURE_DOWNLOAD_TOKEN | Token configuration on SDM |
| always | Always executed (credential collection) |

#### Play-by-play explanation

- Set credentials for SDM appliance
  Hosts: localhost
  Tags: always

  Purpose:
  - Ensures domain credentials (username, password) are collected using atos.dhc.askPassWrapper if not already defined.

- Get SDM API authentication
  Hosts: localhost
  Tags: always

  Purpose:
  - Retrieves SDM API authentication (sdmGetAuth.yml).

- Get SDM VI domain details
  Hosts: localhost
  Tags: always

  Purpose:
  - Retrieves information about SDM domain lists (queryDomainType: "VI") required by next task (sdmGetDomains.yml).

- Check Depot Status and reconfigure if needed
  Tags: VI_DOWNLOAD_BUNDLES, VI_PRECHECK, VI_NSXT_PATCHING, VI_VCS_PATCHING, VI_ESX_PATCHING
  Condition: when: configDepot == true

  Purpose:
  - Validates SDM depot configuration and (optionally) repairs it.
  - Uses sdmDepotConf.yml with depotBeforeCheck=true.

- Check if upgrade and/or patching required
  Tags: VI_DOWNLOAD_BUNDLES, VI_PRECHECK, VI_NSXT_PATCHING, VI_VCS_PATCHING, VI_ESX_PATCHING

  Purpose:
  - Determines which products require patching:
    - nsxPatchingRequired
    - vcsPatchingRequired
    - esxiPatchingRequired

- Download bundles for patching
  Tags: VI_DOWNLOAD_BUNDLES, VI_PRECHECK, VI_NSXT_PATCHING, VI_VCS_PATCHING, VI_ESX_PATCHING
  Condition: filteredPatches | length > 0 AND any patching flag is true

  Purpose:
  - Downloads appropriate bundle(s) for VI domain.
  - Uses role: downloadBundlesPreUpgr.yml.

- Refresh SDM VI domain details
  Hosts: localhost
  Tags: always

  Purpose:
  - Retrieves information about SDM domain lists (queryDomainType: "VI") required by next task (sdmGetDomains.yml).

- Check SDM for running tasks
  Tags: VI_PRECHECK, VI_NSXT_PATCHING, VI_VCS_PATCHING

  Purpose:
  - Prevents patch start if SDM is processing another workflow.

- Check all credentials in SDM
  Tags: VI_PRECHECK, VI_NSXT_PATCHING, VI_VCS_PATCHING
  Condition: runCredsCheck == true

  Purpose:
  - Runs sdmCredentialsCheck.yml to validate all stored credentials.

- Check all certificates in SDM
  Tags: VI_PRECHECK, VI_NSXT_PATCHING, VI_VCS_PATCHING
  Condition: runCertsCheck == true

  Purpose:
  - Runs sdmCertificatesCheck.yml for domain certificates using sdmDomainId.

- Run prechecks before patching
  Tags: VI_PRECHECK, VI_NSXT_PATCHING, VI_VCS_PATCHING
  Condition: runPrechecks == true AND (any patch flag == true)

  Purpose:
  - Executes SDM domain checksets (checkType=UPGRADE) to validate readiness.

- NSX‑T Patching Block
  Tags: VI_NSXT_PATCHING, VI_PATCHING
  Condition: Executed when nsxPatchingRequired == true

  - Send notification on patching process start

    Purpose:
    - Informs recipients that NSX patching has begun.

  - Gather NSX Managers IPs and Credentials

    Purpose:
    - Collects NSX manager endpoints and admin passwords for next tasks.

  - Check NSX for Open Alarms

    Purpose:
    - Ensures cluster is stable before upgrade.

  - Run NSX configuration backup task
    Condition: runNsxBackups == true

    Purpose:
    - Performs configuration backup per NSX Manager.

  - Place NSX components in maintenance mode (Aria Ops)

    Purpose:
    - Places all NSX components under maintenance window.

  - Patch NSX in VI domain

    Purpose:
    - Applies patch type NSX_T_MANAGER for sdmDomainType "VI".

  - Exit NSX maintenance mode

    Purpose:
    - Places all NSX components under normal monitoring state in Aria Operations.
  
  - Validate NSX patch outcome

    Purpose:
    - Re-evaluate nsxPatchingRequired.
    - Hard‑fail if NSX still needs patching.

  - Post-upgrade tasks

    Purpose:
    - Success email sent.
    - Reset vcfPatchingTaskFinished.

- vCenter Patching Block
  Tags: VI_VCS_PATCHING
  Condition: Executed when vcsPatchingRequired == true

  - Send notification on patching process start

    Purpose:
    - Informs recipients that vCenter patching has begun.

  - Read all VMs from VCS002

    Purpose:
    - Needed to identify vcs001 and vcs002 for snapshots.

  - Place both vCenters in maintenance mode (Aria Ops)

    Purpose:
    - Places all vcs00 components under maintenance window.

  - Snapshot vCenter vcs002 (powered off)
    Condition: runVcsBackups == true

    Purpose:
    - Runs VAMI backup and powered off snapshot of vcs002 .

  - vCenter vcs002 health check post snapshot
    Condition: runVcsBackups == true

    Purpose:
    - Runs vcs002 post reboot healthchecks.

  - Snapshot vCenter vcs002 (powered off)
    Condition: runVcsBackups == true

    Purpose:
    - Runs VAMI backup and powered off snapshot of vcs002 .

  - vCenter vcs002 health check post snapshot
    Condition: runVcsBackups == true

    Purpose:
    - Runs vcs002 post reboot healthchecks.

  - Patch vCenter for VI domain

    Purpose:
    - Applies patch type VCENTER.

  - Exit VCENTER maintenance mode

    Purpose:
    - Places all vcs00 components under normal monitoring state in Aria Operations.

  - Validate vCenter patch outcome

    Purpose:
    - Re-evaluate vcsPatchingRequired.
    - Fail if patching still required.

  - Post-upgrade tasks

    Purpose:
    - Success email.
    - Reset vcfPatchingTaskFinished.

- ESX patching block
  Tags: VI_ESX_PATCHING
  Condition: Executed when esxPatchingRequired == true

  - Place ESXi and Avamar/Networker appliances in maintenance mode in Aria Ops

    Purpose:
    - Places all cmp00, avp00 and nvp00 components under maintenance window.

  - Check and shutdown avamar and networker vms

    Purpose:
    - Shuts down backup vms (avamar or networker).

  - Create cluster image and import it to SDDC Manager

    Purpose:
    - Creates host image used for patching.

  - Patch ESXi hosts in VI domain

    Purpose:
    - Executes ESXi hosts patching in "VI" domain.

  - Check and power on avamar and networker vms

    Purpose:
    - Powers on backup vms (avamar or networker).

  - Exit ESXi/Backup VMs maintenance mode

    Purpose:
    - Places ESXi and Avamar/Networker appliances from maintenance mode in Aria Operations.

- Post-upgrade tasks
  Tags: VI_ESX_PATCHING

  Purpose:
  - Success email.
  - Reset vcfPatchingTaskFinished.

### VCF upgrade - Workload domain witness host patching

**Imported Playbook**: `lifecycleUpdateVsanWitness.yml`

**Main TAG**: `VI_WITNESS_UPGRADE`

#### Playbook summary

This playbook automates full lifecycle operations of the Workload Domain Witness Host. It handles Witness ESXi host patching. Tasks include SDM and vCenter authentication, prechecks, patch execution and post‑upgrade cleanup.

#### Playbook Requirements

- Domain credentials (username, password).
- Working API connectivity to SDDC Manager and vCenters.

#### Table of tags

| Tag name | Description |
| ---------- | ----------- |
| VI_WITNESS_UPGRADE | Workload Domain vSAN witness host patch flow |
| always | Always executed (credential collection) |

#### Play-by-play explanation

- Get SDM API authentication
  Hosts: localhost

  Purpose:
  - Retrieves SDM API authentication (sdmGetAuth.yml).

- Get session for VCS authentication
  Hosts: localhost

  Purpose:
  - Retrieves vCenter session authentication (vcsGetSession.yml).

- Get SDM domain details
  Hosts: localhost

  Purpose:
  - Retrieves information about SDM domain lists required by next task.

- Check cluster type
  Hosts: localhost

  Purpose:
  - Retrieves information about cluster type (isStreched).

- Check if Witness needs upgrade
  Hosts: localhost

  Purpose:
  - Makes a decision if witness patching is required.

- Run prerequisites for vSAN Witness upgrade
  Hosts: localhost

  Purpose:
  - Enables proxy on vCenter.
  - Prepares online depots.
  - Syncs vCenter LCM depots and gets image required for update.
  - Prepares offline bundle for ESXi host.

- Upgrade Witness Appliance
  Hosts: localhost

  Purpose:
  - Upgrades witness appliance.

- Delete Image cluster created for upgrade
  Hosts: localhost

  Purpose:
  - Cleanups requirements created on vCenter (temporary cluster required for image creation).

- Remove Proxy on VCS
  Hosts: localhost

  Purpose:
  - Disables proxy on vCenter.

### VCF upgrade - Supervisors patching

**Imported Playbook**: `lifecycleUpdateSupervisors.yml`

**Main TAG**: `N/A`

#### Playbook summary

This playbook automates full lifecycle operations of the supervisor enabled clusters on management and workload vCenters. Tasks include vCenter authentication, prechecks and cluster upgrade.

#### Playbook Requirements

- Domain credentials (username, password).
- Working API connectivity to vCenters.

#### Table of tags

| Tag name | Description |
| ---------- | ----------- |
| MGMT_SUPERVISOR_UPGRADE | Management vCenter Supervisor clusters patch flow |
| VI_SUPERVISOR_UPGRADE | Workload vCenter Supervisor clusters patch flow |
| always | Always executed (credential collection) |

#### Play-by-play explanation

- Get session for VCS authentication
  Hosts: localhost

  Purpose:
  - Retrieves VCS authentication (vcsGetSession.yml).

- Get all available updates for a clusters
  Hosts: localhost

  Purpose:
  - Retrieves all available supervisor updates list.

- Get the highest available update version for supervisor
  Hosts: localhost

  Purpose:
  - Filters the highest available Supervisor version.

- Get supervisor enabled clusters
  Hosts: localhost

  Purpose:
  - Retrieves a list of supervisor-enabled clusters.

- Create list of clusters with available upgrade versions
  Hosts: localhost

  Purpose:
  - Creates a list of supervisor clusters upgrade candidates.

- Show clusters available for upgrade
  Hosts: localhost

  Purpose:
  - Shows all clusters available for update.

- Set required variables for upgrade
  Hosts: localhost

  Purpose:
  - Selects first cluster on the list for upgrade and prepares input variables for upgrade task.

- Update cluster
  Hosts: localhost

  Purpose:
  - Trigger supervisor update
  - Checks update progress.

- Supervisor cluster upgrade notice
  Hosts: localhost

  Purpose:
  - Informative - shows success notice.

- Supervisor not enabled notice
  Hosts: localhost

  Purpose:
  - Informative - shows an info if supervisor is not enabled on given cluster.
