
# Automated process of Lifecycle Management - 2.0.1.1

## Table of Contents
  
## List of Changes

| Date       | Issue    | Author          | TOS  | Description |
| ---------- | -------- | --------------- | ---- | --------------------- |
| 31/07/2025 | VCS-15983 | Mariusz Stanek |      | Initial version |
| 24/10/2025 | VCS-17317 | Mariusz Stanek |      | Final version |

## Introduction

This document describes automated process of Lifecycle Management from VCS-1.8.4 to VCS-2.0.1.1.

## Scope

Scope of this Work Instruction covers whole process of updating VCS from version 1.8.4 to 2.0.1.1 in automated fashion.
Automation process covers sections covered in [wiLifeCycleManagement-DHC2.0.1](wiLifeCycleManagement-DHC2.0.1.md) and [dhcVcfUpgradeTo-5.2.0.md](dhcVcfUpgradeTo-5.2.0.md), which are:

- VCF upgrade - management domain.
- Billing migration.
- VCF upgrade - workload domain.
- VCF upgrade - distributed switch.
- Aria Automation multitenancy enablement.
- Testing Infra,vRA.
- Hardening.

## Related Documents

| Document |
| -------- |
| [Lifecycle Management - 2.0.1](wiLifeCycleManagement-DHC2.0.1.md) |
| [VCF Upgrade to 5.2.0](dhcVcfUpgradeTo-5.2.0.md) |

## Prerequisites

- It is expected the upgrade is performed by a person(s) with expert knowledge in VMware, Linux and VCS solution. Engineers must have sufficient privileges.
- Image backups are created and available.
- All accounts are valid (not locked due, for example expired password).
- The playbooks mentioned in this work instruction, unless otherwise specified (for example user *next*), are executed by an engineer logged in with their dedicated domain account.
- File `csi-gcp-access.json` required for SCP to GCP migration was received from CSI Team (<dl-cloud-csi@atos.net>). For more details read [DHC METERING AND RECHARGING CUSTOMER ONBOARDING WI](https://atos365.sharepoint.com/:b:/s/CloudServiceInfrastructure-CSI/Eb0v-97tcN5Gsqoia3JOOA0BvjigQkw_PjTsjaCTfaOiaA?e=nYWdxI&xsdata=MDV8MDJ8bWFyaXVzei5zdGFuZWtAYXRvcy5uZXR8NGMxMTJlNmRhMmFiNDQzN2RhODAwOGRkODIzYWQxNDl8MzM0NDBmYzZiN2M3NDEyY2JiNzMwZTcwYjAxOThkNWF8MHwwfDYzODgwOTkxMTMwODMwMjgwMHxVbmtub3dufFRXRnBiR1pzYjNkOGV5SkZiWEIwZVUxaGNHa2lPblJ5ZFdVc0lsWWlPaUl3TGpBdU1EQXdNQ0lzSWxBaU9pSlhhVzR6TWlJc0lrRk9Jam9pVFdGcGJDSXNJbGRVSWpveWZRPT18MHx8fA%3d%3d&sdata=bDc5UkFxaHYzdU1FdzJydUlwbEdGbFBQL2FBTnVTSFBQbSs1bjNvbDllUT0%3d) and [isDedicated.pdf](files/wiLifeCycleManagementDHC201/isDedicated.pdf)
- Upgrade process for vCenter requires one temporary IP address. By default it is .40 from vCenter management network. Please ensure that this IP address is not used. In case IP is used please choose another one free from the same network and provide it in extra vars file mentioned further in this document for example "vcsTempIp": "10.0.0.0".
- During migration it is required to have new licenses, see VMware Cloud Foundation Upgrade Prerequisites. According to the information received from Global VMware TAM for Atos the license keys will need an upgrade (vSphere / VSAN 8, NSX 4) through the Broadcom portal by the Atos Contract Administration Team. License keys for vCenter, VSAN and ESXi have to be insterted in extra vars file.

## Hardware and firmware compatibility

It is required to check hardware/firmware compatibility during upgrade preparations. It can be also required to contact with hardware vendor if there are any concerns with compatible version.

Below links can help during compatibility check process:

1. [ESXi 8.0 Hardware requirements](https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/8-0/esxi-upgrade-8-0/upgrading-esxi-hosts-upgrade/esxi-requirements-upgrade/esxi-hardware-requirements-upgrade.html)
2. [CPU Support Deprecation and Discontinuation In vSphere Releases](https://knowledge.broadcom.com/external/article?legacyId=82794)
3. [Using the VMware Product Interoperability Matrixes](https://knowledge.broadcom.com/external/article/343230/using-the-vmware-product-interoperabilit.html)

General Broadcom compatibility guides:

1. [Systems/Servers](https://compatibilityguide.broadcom.com/search?program=server&persona=live&column=partnerName&order=asc)
2. [CPU series](https://compatibilityguide.broadcom.com/search?program=cpu&persona=live&column=cpuSeries&order=asc)
3. [Storage/SAN](https://compatibilityguide.broadcom.com/search?program=san&persona=live&column=partnerName&order=asc)
4. [IO Devices](https://compatibilityguide.broadcom.com/search?program=io&persona=live&column=brandName&order=asc)
5. [Guest OS](https://compatibilityguide.broadcom.com/search?program=software&persona=live&column=osVendors&order=asc)

## Mandatory Extra Vars

Upgrade playbook requires some extra vars to be provided during playbook execution. Most convenient way of supplying these extra vars is by putting them in a .json file. Below is the list of mandatory extra vars parameters already in the json format. Copy this content into new .json file on the ans001 server where the playbook will be executed, i.e. vars.json and fill in all the parameters. File can be stored in your home directory or anywhere else where ansible can access it and read it.

```json
{

    "vcfBundleDownloadToken": "insert download token here",
    "licenseKeyVcenter": "insert vCenter license here",
    "licenseKeyVsan": "insert VSAN license here",
    "licenseKeyEsxi": "insert ESXi license here",
    "vraTenants": ["tenant1", "tenant2", "tenant3"],
    (optional)"targetPatchVcenter": "for example 8.0.3.00400-24322831",
    (optional)"targetPatchNsxt": "for example 4.2.2.0.0-24730888",
    (optional)"targetPatchEsxi": "for example 8.0.3-24674464",
    (optional)"vcsTempIp": "for example 10.0.0.0"

}
```

Optional parameters must be provided when patching must be done to some defined version. If not then latest available version will be used.

## Email notification functionality

Automated upgrade playbook can send email notification upon successful or failed upgrade execution. In order to work it requires providing SRS server FQDN and email notification recepients. Prompts to provide these parameters are described in this document in following sections if this document.

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

## DHC Version matrix

DHC Version matrix is automatically updated by python script described in following sections if this document.

## Rollback

Rollback is partially possible when snapshot is available for particular upgraded element.

## Upgrade Steps

### Code change and ans001 upgrade

In VCS-2.0.1.1 automated upgrade part of steps is done by python script `upgradeDhc201Prereq.sh`. These steps are:

- Backup ansible code and download code provided by TAG in python prompt. Update, deploy, manage, dhc-collections and version-matrix are automatically upgraded with proper code provided by TAG.
- Configure ansible.cfg file to enable automated email notifications.
- Move ansible group_vars and inventory.
- Upgrade ansible python virtual environment.
- Change log permission for ansible.

To prepare environment for code change and ans001 upgrade login to `ans001` on `next` user and execute below commands:

```bash
cd /opt/dhc/update
git pull
git checkout DHC-1.8
```

Ensure that file `upgradeDhc201Prereq.sh` is present in `/opt/dhc/update` and execute this file:

```bash
bash  ./upgradeDhc201Prereq.sh && source /usr/local/bin/py3venv/current/bin/activate
```

Python script will ask to to provide some inputs:

```bash
    "Provide domain username: " DOMAIN_USER
    "Provide domain password (input will be hidden): " DOMAIN_USERPASS
    "Provide Git Username (not the email address): " GIT_USER
    "Provide Git Token (input will be hidden): " GIT_TOKEN
    "Provide DHC Git Tag: " DHC_TAG 
    "Provide email notification recepients. If more then one use comma (,) to separate: " EMAIL_RECEPIENTS
    "Provide FQDN of SRS server, ie: gre28srs001.vx8dch01.next: " SRS_SERVER
```

### Upgrade using upgrade automation

Upgrade proces can be done in two scenarios detaily described in Section `Automated upgrade scenarios`.

- First scenario: `all-at-once` - run entire upgrade process in one strike (no ansible tags required).
- Second scenario: `step-by-step` - granular divided into controlled stages (ansible tags requirted).

To run upgrade using upgrade automation it is required navigate to `/opt/dhc/update` on the `ans001` server and launch upgrade playbook with extra vars parameter and proper ansible tags if you want divide upgrade proces into controlled steps.

Apart from extra vars provided in the file, right after launching playbook it will ask for user credentials (username and password). Once these are provided there will be no more user prompts during playbook execution.  

Upgrade process will take several hours, this can be 6hrs or more depending on environment. Due to that fact make sure your SSH connection to `ans001` server will not be terminated. Configure SSH keep alive packets in your tool of choice for SSH connections. Example: in Putty go to `Connection` and set `Seconds between keepalives` to 60 seconds

## Playbooks structure and ansible TAGs explanation

### Overview

This Section explains structure of playbooks included in `upgradeDhc201Main.yml` and ansible TAGs implemented in each of them.

### Table of playbooks implemented in upgradeDhc201Main.yml playbook

- [Get user credentials](#get-user-credentials)
- [VCF upgrade - management domain](#vcf-upgrade---management-domain)
- [Billing migration](#billing-migration)
- [VCF upgrade - workload domain](#vcf-upgrade---workload-domain)
- [VCF upgrade - distributed switch](#vcf-upgrade---distributed-switch)
- [Aria Automation multitenancy enablement](#aria-automation-multitenancy-enablement)
- [Testing Infra,vRA](#testing-infravra)
- [Hardening](#hardening)

### Get user credentials

**Playbook**: `upgradeDhc201Main.ym`  

#### Playbook overview

Playbook prompts the user for `username` and `password` if not already defined for example in extra vars.

#### Playbook tags

| Tag name | Description | Included tags |
| ---------- | -------- | --------------- |
| always | It runs each time | none |

### VCF upgrade - management domain

**Imported Playbook**: `upgradeDhc201MgmtDomain.yml`
**Main TAG**: `1`

#### Playbook summary

This playbook automates the upgrade and patching of a VMware Cloud Foundation management domain, including all major components (SDDC Manager, NSX-T, vCenter, ESXi, vSAN, and Witness). Tags are used for selective execution and organization of preparation, upgrade, patching, and post-upgrade activities.

#### Playbook Requirements

Depot token credentials for software bundle downloads.

#### Table of tags

| Tag name | Description |
|----------|-------------|
| MGT_UPGRADE_PREP | Management domain upgrade preparation tasks|
| MGT_SQUID_UPDATE | Squid proxy configuration update tasks |
| MGT_CONFIGURE_DOWNLOAD_TOKEN | Configure download tokens on SDM |
| MGT_DOWNLOAD_BUNDLES | Download upgrade bundles tasks |
| MANAGEMENT_DOMAIN_UPGRADE | Main tag for management domain upgrade tasks |
| MGT_VCENTER_UPGRADE_PREP | vCenter upgrade preparation tasks |
| MGT_VCENTER_UPGRADE | vCenter upgrade tasks |
| MGT_IWA_TO_LDAP | Change identity provider from IWA to LDAP |
| MGT_DEPOT_CHECK | Depot status check/configuration |
| MGT_PRE_UPGRADE_CHECK | Pre-upgrade check tasks |
| MGT_SDDC_MANAGER_UPGRADE_PREP | SDDC Manager upgrade preparation |
| MGT_SDDC_MANAGER_UPGRADE | SDDC Manager upgrade tasks |
| MGT_COMPATIBILITY_FLAG | Set compatibility flags |
| MGT_NSXT_UPGRADE_PREP | NSX-T upgrade preparation tasks |
| MGT_NSXT_UPGRADE | NSX-T upgrade tasks |
| MGT_VCS_UPGRADE | vCenter Server upgrade tasks |
| MGT_ESXI_UPGRADE | ESXi host upgrade tasks |
| MGT_RELICENSE |Relicensing after upgrade |
| MGT_APPLY_CONFIG_DRIFT | Apply configuration drift tasks |
| MGT_PATCHING_PREP | Patching preparation tasks |
| MGT_PATCHING | General patching tasks |
| MGT_NSXT_PATCHING |NSX-T patching tasks |
| MGT_VCS_PATCHING | vCenter Server patching tasks |
| MGT_ESXI_PATCHING | ESXi patching tasks |
| MGT_POST_UPGRADE | Post-upgrade tasks |
| MGT_POST_PATCHING | Post-patching tasks |
| MGT_VSAN_UPGRADE | vSAN upgrade tasks |
| MGT_NSXT_TAGGING_DISABLE | Disable NSX-T VM tagging in Aria Automation |
| MGT_WITNESS_UPGRADE | vSAN Witness appliance upgrade tasks |

#### Play-by-play explanation

- Set credentials for SDM appliance

  Hosts: localhost, all

  Tags: always
  
  Purpose:
  - Ensures SDM credentials are set using atos.dhc.askPassWrapper if not already defined.

- Add entries for Broadcom to squid whitelist

  Hosts: pxy002, pxy003
  
  Tags: always, MGT_UPGRADE_PREP, MGT_SQUID_UPDATE
  
  Purpose:
  - Retrieves credentials using atos.dhc.connectionWrapper.
  - Adds Broadcom to the proxy whitelist using dhc-upgradeDhc201.

- Configure download token on SDM

  Hosts: sdm001
  
  Tags: always, MGT_CONFIGURE_DOWNLOAD_TOKEN, MGT_UPGRADE_PREP, MGT_DOWNLOAD_BUNDLES
  
  Purpose:
  - Sets credential variables for SDM.
  - Configures the VCF download token.

- Upgrade Playbook for VCF components

  Hosts: localhost
  
  Tags: MANAGEMENT_DOMAIN_UPGRADE
  
  Purpose:
  - Orchestrates the upgrade of the management domain, including all major components

  Upgrade Preparation
  - Get SDM API authentication:

    Authenticates with SDM to allow further operations.

    Tags: always
  - Get SDM Management domain details:

    Retrieves information about the management domain from SDM.

    Tags: always
  - Change vCenter identity provider from IWA to LDAP:

    Changes vCenter identity provider to LDAP for improved security and compliance.

    Tags: MGT_VCENTER_UPGRADE_PREP, MGT_VCENTER_UPGRADE, MGT_IWA_TO_LDAP, MGT_UPGRADE_PREP
  - Check Depot Status and reconfigure if needed:

    Ensures the depot is correctly configured for bundle downloads.

    Tags: MGT_DEPOT_CHECK, MGT_UPGRADE_PREP, MGT_DOWNLOAD_BUNDLES, always
  - Download bundles for SDM upgrades:

    Downloads upgrade bundles for the management domain.

    Tags: MGT_DOWNLOAD_BUNDLES, MGT_UPGRADE_PREP
  - Refresh SDM Management domain details:

    Updates domain information after bundle download.

    Tags: always
  - Check SDM for running tasks:

    Ensures no conflicting tasks are running before upgrade.

    Tags: always, MGT_PRE_UPGRADE_CHECK
  - Check all credentials in SDM:

    Validates all credentials before upgrade.

    Tags: always, MGT_PRE_UPGRADE_CHECK
  - Check all certificates in SDM:

    Validates all certificates before upgrade.

    Tags: always, MGT_PRE_UPGRADE_CHECK
  - Set upgrade required flags for all products:

    Determines which components require an upgrade.

    Tags: always

  SDDC Manager Upgrade Block
  - Run SDM pre-upgrade precheck:

    Runs pre-upgrade checks for SDDC Manager.

    Tags: MGT_SDDC_MANAGER_UPGRADE_PREP, MGT_SDDC_MANAGER_UPGRADE
  - Place SDDC Manager in maintenance mode in Aria Ops:

    Puts SDDC Manager into maintenance mode for upgrade.

    Tags: MGT_SDDC_MANAGER_UPGRADE
  - Upgrade SDDC Manager in Management domain:

    Performs the SDDC Manager upgrade.

    Tags: MGT_SDDC_MANAGER_UPGRADE
  - Exit SDDC Manager from maintenance mode in Aria Ops:

    Returns SDDC Manager to normal operation.

    Tags: MGT_SDDC_MANAGER_UPGRADE
  - Disable compatibility flag in SDM:

    Disables compatibility flag post-upgrade.

    Tags: MGT_NSXT_UPGRADE, MGT_VCENTER_UPRGADE, MGT_ESXI_UPGRADE, MGT_COMPATIBILITY_FLAG, MGT_SDDC_MANAGER_UPGRADE

  Download Bundles for Remaining Upgrades
  - Configure VCF download token:

    Configures download token for further upgrades.

    Tags: MGT_DOWNLOAD_BUNDLES
  - Check Depot Status and reconfigure if needed:

    Ensures depot is ready for further upgrades.

    Tags: MGT_DOWNLOAD_BUNDLES
  - Download bundles for remaining upgrades and patching:

    Downloads additional bundles for patching and upgrades.

    Tags: MGT_DOWNLOAD_BUNDLES

  Prechecks Before Upgrade
  - Run prechecks before upgrade:

    Executes pre-upgrade checks for NSX-T, vCenter, and ESXi if required.

    Tags: MGT_NSXT_UPGRADE_PREP, MGT_PRE_UPGRADE_CHECK, MGT_NSXT_UPGRADE, MGT_VCS_UPGRADE, MGT_ESXI_UPGRADE

  NSX-T Upgrade Block
  - Gather NSX Managers IPs and Credentials:

    Collects NSX manager credentials for the upgrade.

    Tags: MGT_NSXT_UPGRADE_PREP, MGT_NSXT_UPGRADE
  - Check NSX for Open Alarms:

    Checks for any open alarms on NSX managers before proceeding.

    Tags: MGT_NSXT_UPGRADE_PREP, MGT_NSXT_UPGRADE
  - Run NSX configuration backup task:

    Backs up NSX configuration before upgrade.

    Tags: MGT_NSXT_UPGRADE_PREP, MGT_NSXT_UPGRADE
  - Place NSX components in maintenance mode in Aria Ops:

    Puts NSX components into maintenance mode to safely perform the upgrade.

    Tags: MGT_NSXT_UPGRADE
  - Run NSXT upgrade:

    Performs the NSX-T upgrade for the management domain.

    Tags: MGT_NSXT_UPGRADE
  - Exit NSX components from maintenance mode in Aria Ops:

    Returns NSX components to normal operation after upgrade.

    Tags: MGT_NSXT_UPGRADE
  
  vCenter Upgrade Block
  - Place VCS in maintenance mode in Aria Ops:

    Puts vCenter in maintenance mode for upgrade.

    Tags: MGT_VCS_UPGRADE
  - Read all VMs from VCS001:

    Retrieves all VMs managed by vCenter for snapshot and health checks.

    Tags: MGT_VCS_UPGRADE
  - Snapshot VI domain vCenter (powered off):

    Creates a snapshot of the vCenter VM before upgrade.

    Tags: MGT_VCS_UPGRADE
  - VCS health check post snapshot:

    Checks vCenter health after snapshot.

    Tags: MGT_VCS_UPGRADE
  - Run vCenter upgrade:

    Upgrades vCenter for the management domain.

    Tags: MGT_VCS_UPGRADE
  - Exit VCS from maintenance mode in Aria Ops:

    Returns vCenter to normal operation after upgrade.

    Tags: MGT_VCS_UPGRADE

  ESXi Upgrade Block
  - Place ESXi and Avamar/Networker appliances in maintenance mode in Aria Ops:

    Puts ESXi hosts and backup appliances into maintenance mode.

    Tags: MGT_ESXI_UPGRADE
  - Check and shutdown avamar and networker VMs:

    Shuts down backup VMs before ESXi upgrade.

    Tags: MGT_ESXI_UPGRADE
  - Run ESXi upgrade:

    Upgrades ESXi hosts in the management domain.

    Tags: MGT_ESXI_UPGRADE
  - Check and power on avamar and networker VMs:

    Powers on backup VMs after ESXi upgrade.

    Tags: MGT_ESXI_UPGRADE
  - Exit ESXi and Avamar/Networker appliances from maintenance mode in Aria Ops:

    Returns ESXi hosts and backup appliances to normal operation.

    Tags: MGT_ESXI_UPGRAD

  Relicensing
  - Run relicense:

    Relicenses vCenter, vSAN, and ESXi after upgrade.

    Tags: MGT_RELICENSE, MGT_POST_UPGRADE

  Configuration Drift
  - Apply configuration drift:

    Applies configuration drift corrections post-upgrade.

    Tags: MGT_APPLY_CONFIG_DRIFT, MGT_POST_UPGRADE

  Patching Preparation and Execution
  - Run prechecks before patching:

    Executes pre-patching checks for NSX-T, vCenter, and ESXi as required.

    Tags: MGT_PATCHING_PREP, MGT_PATCHING, MGT_NSXT_PATCHING, MGT_VCS_PATCHING, MGT_ESXI_PATCHING
  - NSX-T Patching:

    Gather NSX credentials, backup configuration, place in maintenance mode, patch NSX, and exit maintenance mode.

    Tags: MGT_NSXT_PATCHING, MGT_PATCHING
  - vCenter Patching Block:

    Place vCenter in maintenance mode, snapshot VMs, health check, patch vCenter, and exit maintenance mode.

    Tags: MGT_VCS_PATCHING, MGT_PATCHING
  - ESXi Patching Block:

    Place ESXi and backup appliances in maintenance mode, shutdown backup VMs, patch ESXi, power on backup VMs, and exit maintenance mode.
  
    Tags: MGT_ESXI_PATCHING, MGT_PATCHING

  Post-Patching Steps
  - Apply configuration drift:

    Applies configuration drift corrections post-patching.

    Tags: MGT_APPLY_CONFIG_DRIFT, MGT_POST_PATCHING
  - Run vSAN upgrade:

    Upgrades vSAN after patching.

    Tags: MGT_VSAN_UPGRADE, MGT_POST_PATCHING

  NSX-T Tagging Disable
  - Disable NSX-T VM tagging in Aria Automation:

    Disables NSX-T VM tagging for each tenant in Aria Automation.

    Tags: MGT_NSXT_TAGGING_DISABLE

- Upgrade Witness Appliance for MANAGEMENT

  Hosts: vwa001

  Tags: MGT_WITNESS_UPGRADE

  Purpose:
  - Sets credentials from transfer host.
  - Checks cluster type and witness version.
  - Runs prerequisites and upgrades the vSAN Witness appliance if required.

### Billing migration

**Imported Playbook**: `upgradeBilling201.yml`
**Main TAG**: `2`

#### Playbook summary

This playbook automates tasks involved in migrating billing services from `bil001` to `ans001`, switching from SCP-based access to GCP bucket access, validating billing status, and applying necessary configurations (e.g., NSX firewall, package installations, and secrets handling).

##### Playbook requirements

Playbook requires two files to be present in running user home `ans001` directory:

- Load variables from `ansible-vars.json`, if file is not present then playbook run will be interrupted by prompts. Example file content visible below.

```bash
(1040-std) a581913@gre25ans001:~$ pwd
/home/a581913
(1040-std) a581913@gre25ans001:~$ cat ansible-vars.json
{
  "userAnswer": "yes",
  "isDedicatedDhc": "yes",
  "tenantCode": "vx7",
  "countryCode": "none",
  "environmentCode": "none"
}
(1040-std) a581913@gre25ans001:~$
```

- File `csi-gcp-access.json` required for SCP to GCP migration which was received from CSI Team (<dl-cloud-csi@atos.net>). For more details read [DHC METERING AND RECHARGING CUSTOMER ONBOARDING WI](https://atos365.sharepoint.com/:b:/s/CloudServiceInfrastructure-CSI/Eb0v-97tcN5Gsqoia3JOOA0BvjigQkw_PjTsjaCTfaOiaA?e=nYWdxI&xsdata=MDV8MDJ8bWFyaXVzei5zdGFuZWtAYXRvcy5uZXR8NGMxMTJlNmRhMmFiNDQzN2RhODAwOGRkODIzYWQxNDl8MzM0NDBmYzZiN2M3NDEyY2JiNzMwZTcwYjAxOThkNWF8MHwwfDYzODgwOTkxMTMwODMwMjgwMHxVbmtub3dufFRXRnBiR1pzYjNkOGV5SkZiWEIwZVUxaGNHa2lPblJ5ZFdVc0lsWWlPaUl3TGpBdU1EQXdNQ0lzSWxBaU9pSlhhVzR6TWlJc0lrRk9Jam9pVFdGcGJDSXNJbGRVSWpveWZRPT18MHx8fA%3d%3d&sdata=bDc5UkFxaHYzdU1FdzJydUlwbEdGbFBQL2FBTnVTSFBQbSs1bjNvbDllUT0%3d) and [isDedicated.pdf](files/wiLifeCycleManagementDHC201/isDedicated.pdf).

#### Table of tags

| Tag name | Description |
| ---------- | -------- |
| BIL_ANS_MIGRATE | Runs entire process of SCP to GCP migration on bil001 and moves billing to ans001 |
| BIL_SCP_HEALTH_BIL001 | Performs a bil001 healthcheck based on SCP |
| BIL_ANS_MIGRATE_GCP | Migrates billing from SCP to GCP method on bil001 |
| BIL_GCP_HEALTH_BIL001 | Performs a bil001 healthcheck based on GCP |
| BIL_ANS_MIGRATE_PREP | Gathers required packages and insalls them, create snapshots, creates billing-user and Vault entry |
| BIL_ANS_MIGRATE_FILES | Copies billing scripts from bil001 to ans001 /tmp |
| BIL_ANS_MIGRATE_FINAL | Moves copied files to final directory, configures billing, removes temporary file, power off bil001 |
| BIL_ANS_MIGRATE_DFW | Adjusts NSX-T DFW recofiguration to reflect bil001 to ans001 billing migration |
| BIL_GCP_HEALTH_ANS001 | Performs an ans001 healthcheck based on GCP |

#### Play-by-play explanation

- Gather credentials

  Hosts: localhost

  Tags: always

  Purpose:
  - Ensures credentials are available using atos.dhc.askPassWrapper.
  - Adds a transfer host with credentials for later use.

- Check SCP billing status on remote machines

  Hosts: bil001
  
  Tags: BIL_ANS_MIGRATE, BIL_SCP_HEALTH_BIL001
  
  Purpose:
  - Triggers a health check for billing using SCP on bil001.

- Update billing key

  Hosts: bil001
  
  Tags: BIL_ANS_MIGRATE, BIL_ANS_MIGRATE_GCP
  
  Purpose:
  - Reads configuration from a JSON file if present.
  - Prompts user for confirmation and environment details.
  - Loads GCP credentials and creates a vault entry.
  - Sets GCP bucket name based on environment.
  - Prepares for migration by creating a snapshot and installing Google Cloud packages.
  - Copies and updates billing scripts and configuration.

- Check GCP billing status on remote machines

  Hosts: bil001
  
  Tags: BIL_ANS_MIGRATE, BIL_GCP_HEALTH_BIL001

  Purpose:
  - Triggers a health check for billing using GCP on bil001.

- Get needed packages from versionMatrix

  Hosts: localhost
  
  Tags: BIL_ANS_MIGRATE, BIL_ANS_MIGRATE_PREP
  
  Purpose:
  - Retrieves required apt and Python package versions for billing from a version matrix.

- Pre-tasks done on ans001

  Hosts: localhost
  
  Tags: BIL_ANS_MIGRATE, BIL_ANS_MIGRATE_PREP
  
  Purpose:
  - Creates a snapshot for migration.
  - Installs required packages.
  - Adds billing user and stores credentials in vault.

- Copy files from bil001

  Hosts: bil001
  
  Tags: BIL_ANS_MIGRATE, BIL_ANS_MIGRATE_FILES
  
  Purpose:
  - Gets credentials and copies data from bil001 for migration.

- Final tasks done on ans001

  Hosts: localhost
  
  Tags: BIL_ANS_MIGRATE, BIL_ANS_MIGRATE_FINAL
  
  Purpose:
  - Places bil001 in maintenance mode.
  - Runs a series of tasks to finalize migration: interpreter fixes, user swaps, script copies, symlink creation, cron setup, logrotate configuration, SSH modifications, temp cleanup, moves GCP JSON, and shuts down bil001.

- NSX DFW changes for billing

  Hosts: localhost
  
  Tags: BIL_ANS_MIGRATE, BIL_ANS_MIGRATE_DFW
  
  Purpose:
  - Sets up NSX variables and credentials.
  - Updates NSX Distributed Firewall rules for billing communication.

- Check GCP billing status on ans001

  Hosts: localhost

  Tags: BIL_ANS_MIGRATE, BIL_GCP_HEALTH_ANS001
  
  Purpose:
  - Triggers a health check for billing using GCP on ans001.

### VCF upgrade - workload domain

**Imported Playbook**: `upgradeDhc201WorkloadDomain.yml`
**Main TAG**: `3`

#### Playbook summary

This playbook automates the upgrade process of the VI workload domain. It covers components such as NSX-T, vCenter (VCS), ESXi hosts, vSAN, and the Witness Appliance. It handles bundle downloads, upgrade planning, snapshot creation, configuration backups, pre- and post-checks, maintenance mode handling, and patching operations.

#### Playbook requirements

No specific external files are mandatory to be present in the home directory. However, user credentials (e.g., `username`, `password`, and `vcfBundleDownloadToken`) may be prompted if not provided as variables.

#### Table of tags

| Tag name | Description |
|----------|-------------|
| always | Ensures the task/play runs regardless of tags specified at runtime |
| VI_DOMAIN_UPGRADE | Main tag for workload domain upgrade tasks |
| VI_UPGRADE_PREP | Preparation steps for workload domain upgrade |
| VI_DOWNLOAD_BUNDLES | Download upgrade bundles for workload domain |
| VI_NSXT_UPGRADE_PREP | NSX-T upgrade preparation tasks |
| VI_NSXT_UPGRADE | NSX-T upgrade tasks |
| VI_VCS_UPGRADE_PREP | vCenter upgrade preparation tasks |
| VI_VCS_UPGRADE | vCenter upgrade tasks |
| VI_ESXI_UPGRADE_PREP | ESXi upgrade preparation tasks |
| VI_ESXI_UPGRADE | ESXi upgrade tasks |
| VI_VSAN_UPGRADE | vSAN upgrade tasks |
| VI_APPLY_CONFIG_DRIFT | Apply configuration drift tasks |
| VI_RELICENSE | Relicensing after upgrade |
| VI_PATCHING_PREP | Preparation for patching |
| VI_PATCHING | General patching tasks |
| VI_NSXT_PATCHING | NSX-T patching tasks |
| VI_VCS_PATCHING | vCenter patching tasks |
| VI_ESXI_PATCHINGESXi | patching tasks |
| VI_PRECHECK_PATCHING | Pre-patching check tasks |
| VI_POST_UPGRADE | Post-upgrade tasks |
| VI_POST_PATCHING | Post-patching tasks |
| VI_WITNESS_UPGRADE | vSAN Witness appliance upgrade tasks |

#### Play-by-play explanation

- Upgrade Workload Domain

  Hosts: localhost, vwa002
  
  Tags: VI_DOMAIN_UPGRADE
  
  Purpose:
  - Orchestrates the upgrade of the workload domain, including all major components.

  Upgrade Preparation tasks
  - Provide username and password:

    Uses atos.dhc.askPassWrapper to ensure credentials are available if not already defined.

    Tags: always
  - Read SDM details from version matrix:

    Loads SDM version information from the version matrix JSON file for upgrade planning.

    Tags: always
  - Get SDM API authentication:
  
    Authenticates with SDM to allow further operations.

    Tags: always
  - Get Workload domain details:

    Retrieves information about the VI (Workload) domain from SDM.

    Tags: always
  - Set upgrade required flags for all products:

    Determines which components require an upgrade.

    Tags: always
  - Download bundles for upgrade:

    Downloads the necessary upgrade bundles for the VI domain.

    Tags: VI_DOWNLOAD_BUNDLES, VI_UPGRADE_PREP
  - Refresh domainId after bundle download:

    Updates domain information after bundle download.

    Tags: always
  - Create upgrade plan for Workload Domain:

    Creates an upgrade plan targeting the correct VCF version, based on the version matrix.
  
    Tags: VI_NSXT_UPGRADE_PREP, VI_NSXT_UPGRADE, VI_VCS_UPGRADE_PREP, VI_VCS_UPGRADE, VI_ESXI_UPGRADE_PREP, VI_ESXI_UPGRADE, VI_UPGRADE_PREP
  - Run prechecks before upgrade:

    Executes pre-upgrade checks for NSX-T, vCenter, and ESXi if any of them require an upgrade.

    Tags: VI_NSXT_UPGRADE_PREP, VI_NSXT_UPGRADE, VI_VCS_UPGRADE_PREP, VI_VCS_UPGRADE, VI_ESXI_UPGRADE_PREP, VI_ESXI_UPGRADE, VI_UPGRADE_PREP
  
  NSX-T Upgrade Block
  - Gather NSX Managers IPs and Credentials:

    Collects NSX manager credentials for the upgrade.

    Tags: VI_NSXT_UPGRADE_PREP, VI_NSXT_UPGRADE
  - Check NSX for Open Alarms:

    Checks for any open alarms on NSX managers before proceeding.

    Tags: VI_NSXT_UPGRADE_PREP, VI_NSXT_UPGRADE
  - Run NSX configuration backup task:

    Backs up NSX configuration before upgrade.

    Tags: VI_NSXT_UPGRADE_PREP, VI_NSXT_UPGRADE

  - Place NSX components in maintenance mode in Aria Ops:

    Puts NSX components into maintenance mode to safely perform the upgrade.

    Tags: VI_NSXT_UPGRADE
  - Run NSXT upgrade:

    Performs the NSX-T upgrade for the VI domain.

    Tags: VI_NSXT_UPGRADE

  - Exit NSX components from maintenance mode in Aria Ops:

    Returns NSX components to normal operation after upgrade.

    Tags: VI_NSXT_UPGRADE

  vCenter Upgrade Block
  - Place VCS in maintenance mode in Aria Ops:

    Puts vCenter in maintenance mode for upgrade.

    Tags: VI_VCS_UPGRADE

  - Read all VMs from VCS001:

    Retrieves all VMs managed by vCenter for snapshot and health checks.

    Tags: VI_VCS_UPGRADE

  - Snapshot VI domain vCenter (powered off):

    Creates a snapshot of the vCenter VM before upgrade.

    Tags: VI_VCS_UPGRADE

  - VCS health check post snapshot:

    Checks vCenter health after snapshot.

    Tags: VI_VCS_UPGRADE
  - Run vCenter upgrade:

    Upgrades vCenter for the VI domain.

    Tags: VI_VCS_UPGRADE

  - Exit VCS from maintenance mode in Aria Ops:

    Returns vCenter to normal operation after upgrade.

    Tags: VI_VCS_UPGRADE

  ESXi Upgrade Block
  - Place ESXi and Avamar/Networker appliances in maintenance mode in Aria Ops:

    Puts ESXi hosts and backup appliances into maintenance mode.

    Tags: VI_ESXI_UPGRADE
  - Check and shutdown avamar and networker VMs:

    Shuts down backup VMs before ESXi upgrade.

    Tags: VI_ESXI_UPGRADE
  - Run ESXi upgrade:

    Upgrades ESXi hosts in the VI domain.

    Tags: VI_ESXI_UPGRADE
  - Check and power on avamar and networker VMs:

    Powers on backup VMs after ESXi upgrade.

    Tags: VI_ESXI_UPGRADE
  - Exit ESXi and Avamar/Networker appliances from maintenance mode in Aria Ops:

    Returns ESXi hosts and backup appliances to normal operation.

    Tags: VI_ESXI_UPGRADE

  vSAN Upgrade

  - Run VSAN upgrade:

    Upgrades vSAN in the VI domain after ESXi upgrade.

    Tags: VI_VSAN_UPGRADE, VI_POST_UPGRADE

  Configuration Drift

  - Apply configuration drift:

    Applies configuration drift corrections post-upgrade.

    Tags: VI_APPLY_CONFIG_DRIFT, VI_POST_UPGRADE

  Relicensing

  - Run relicense:

    Relicenses vCenter, vSAN, and ESXi after upgrade.

    Tags: VI_RELICENSE, VI_POST_UPGRADE

  Patching Preparation and Execution

  - Run prechecks before patching:

    Executes pre-patching checks for NSX-T, vCenter, and ESXi as required.

    Tags: VI_PATCHING_PREP, VI_PATCHING, VI_NSXT_PATCHING, VI_VCS_PATCHING, VI_ESXI_PATCHING, VI_PRECHECK_PATCHING

  - NSX-T Patching:

    Gather NSX credentials, backup configuration, place in maintenance mode, patch NSX, and exit maintenance mode.

    Tags: VI_NSXT_PATCHING, VI_PATCHING

  - vCenter Patching:

    Place vCenter in maintenance mode, snapshot VMs, health check, patch vCenter, and exit maintenance mode.

    Tags: VI_VCS_PATCHING, VI_PATCHING

  - ESXi Patching:

    Place ESXi and backup appliances in maintenance mode, shutdown backup VMs, patch ESXi, power on backup VMs, and exit maintenance mode.

    Tags: VI_ESXI_PATCHING, VI_PATCHING

  Post-Patching Steps

  - Apply configuration drift:

    Applies configuration drift corrections post-patching.

    Tags: VI_APPLY_CONFIG_DRIFT, VI_POST_PATCHING

  - Run VSAN upgrade:

    Upgrades vSAN after patching.

    Tags: VI_VSAN_UPGRADE, VI_POST_PATCHING

- Upgrade Witness Appliance for VI

  Hosts: vwa002
  
  Tags: VI_WITNESS_UPGRADE
  
  Purpose:
  - Checks cluster type and witness version.
  - Runs prerequisites and upgrades the vSAN Witness appliance if required.
  - Deletes the image cluster created for upgrade if conditions are met.

### VCF upgrade - distributed switch

**Imported Playbook**: `upgradeDistributedSwitch.yml`
**Main TAG**: `4`

#### Playbook overview

This playbook automates the upgrade of VMware vSphere Distributed Switches (VDS) across multiple vCenters. It loads required variables, ensures credentials are available, retrieves the target VDS version from a version matrix, securely fetches vCenter credentials, and then performs the upgrade for each vCenter in the environment. The playbook is straightforward and does not use custom tags, so all steps are executed in sequence.

#### Playbook requirements

No additional requirements

#### Table of tags

| Tag name | Description |
|----------|-------------|
| (none) | This playbook does not use explicit tags. All tasks are always run |

#### Play-by-Play explanation

No additonal explanation required.

### Aria Automation multitenancy enablement

**Imported Playbook**: `upgradeDhc201VraMultitenancy.yml`
**Main TAG**: `5`

#### Playbook overview

This playbook automates the enablement of multitenancy in VMware Aria Automation (vRA). It safely places the vRA appliance into maintenance mode, resizes the node(s) as needed, configures multitenancy, and then returns the appliance to normal operation. Tags are used to organize and selectively execute tasks related to resizing, multitenancy enablement, and configuration.

#### Playbook requirements

No additonal requirements.

#### Table of tags

| Tag name | Description |
| ---------- | -------- |
| VRA_RESIZE | Tasks related to resizing vRA nodes |
| VRA_MULTITENANCY | Main tag for vRA multitenancy enablement |
| VRA_CONFIGURE_MULTITENANCY | Tasks for configuring vRA multitenancy |

#### Play-by-play explanation

- VRA Multitenancy Enablement – Enable Maintenance Mode

  Hosts: localhost
  
  Tags: VRA_RESIZE, VRA_MULTITENANCY, VRA_CONFIGURE_MULTITENANCY

  Purpose:
  - Places the vRA appliance into maintenance mode in Aria Operations (Aria Ops) to safely perform configuration changes.
  - Uses the dhc-configureVropsMaintenance role with maintenanceAction: "STOP" to stop operations on the vRA node for a specified duration (240 minutes).

- Aria Automation Multitenancy Enablement – Update vRA Node Size

  Tags: VRA_RESIZE, VRA_MULTITENANCY
  
  Purpose:
  - Imports and runs the playbook updateVraOnPremNodeSize.yml to resize the vRA node(s) as required for multitenancy.
  - Ensures the vRA infrastructure is appropriately scaled before enabling multitenancy.

- Aria Automation Multitenancy Enablement – Configure vRA MultiTenancy

  Tags: VRA_CONFIGURE_MULTITENANCY, VRA_MULTITENANCY
  
  Purpose:
  - Imports and runs the playbook configureMultiTenancyVraOnPrem.yml to configure vRA for multitenancy.
  - Applies all necessary settings and adjustments to enable multiple tenants within the vRA environment.

- VRA Multitenancy Enablement – Disable Maintenance Mode

  Hosts: localhost
  
  Tags: VRA_RESIZE, VRA_MULTITENANCY, VRA_CONFIGURE_MULTITENANCY

  Purpose:
  - Exits the vRA appliance from maintenance mode in Aria Ops, returning it to normal operation.
  - Uses the dhc-configureVropsMaintenance role with maintenanceAction: "START" to resume operations on the vRA node.

### Testing Infra,vRA

**Imported Playbook**: `upgradeDhc201PostTesting.yml`
**Main TAG**: `6`

#### Playbook overview

This playbook orchestrates post-upgrade or post-change testing for both infrastructure and VMware Aria Automation (vRA) environments. It sequentially runs infrastructure tests, validates vRA catalog items, and checks Day 2 actions to ensure the environment is stable and all automation features are functioning as expected. Tags are used to organize and selectively execute different categories of tests.

#### Playbook requirements

Playbook uses also resources from manage phase repository.

#### Table of tags

| Tag name | Description |
| ---------- | -------- |
| INFRA_TESTING | Infrastructure testing tasks |
| TESTING | General testing tasks |
| VRA_CATALOG_TESTING | vRA catalog item validation tasks |
| VRA_TESTING | General vRA testing tasks |
| VRA_DAY2_TESTING | vRA Day 2 action validation tasks |

#### Play-by-play explanation

- Testing Infra

  Tags: INFRA_TESTING, TESTING

  Purpose:
  - Imports and runs the playbook executeInfraTests.yml to perform infrastructure-level tests.
  - Ensures that the foundational infrastructure is functioning correctly after upgrades or changes.

- Testing vRA Catalog Item

  Tags: VRA_CATALOG_TESTING, VRA_TESTING, TESTING

  Purpose:
  - Imports and runs the playbook validateVraCatalogItem.yml to validate the functionality of vRA catalog items.
  - Checks that catalog items are available, deployable, and behave as expected in the vRA environment.

- Testing vRA Day2Action

  Tags: VRA_DAY2_TESTING, VRA_TESTING, TESTING

  Purpose:
  - Imports and runs the playbook validateVraDay2Action.yml to validate Day 2 actions in vRA.
  - Ensures that post-deployment operations (such as reconfigure, power operations, etc.) work as intended for deployed resources.

### Hardening

**Imported Playbook**: `upgradeDhc201SecurityHardening.yml`
**Main TAG**: `7`

#### Playbook overview

This playbook orchestrates the security hardening of a VMware environment by sequentially applying best practices and remediations to ESXi 8 hosts, vCenter, and Active Directory integration. Each section is tagged for selective execution, allowing targeted or comprehensive security improvements as needed.

#### Playbook requirements

Playbook uses also resources from manage phase repository.

#### Table of tags

| Tag name | Description |
| ---------- | -------- |
| HRD_ESXI | Tasks related to ESXi 8 security hardening |
| HRD_VCENTER | Tasks related to vCenter security remediation |
| HRD_AD | Tasks related to Active Directory hardening |
| HARDENING | General tag for all security hardening tasks |

#### Play-by-play explanation

- Hardening ESXi8

  Tags: HRD_ESXI, HARDENING

  Purpose:
  - Imports and runs the playbook hardeningEsxi8.yml to apply security hardening measures to ESXi 8 hosts.
  - Ensures that ESXi hosts are configured according to best practices and compliance requirements.

- Remediate vCenter Non-Compliant Measures

  Tags: HRD_VCENTER, HARDENING

  Purpose:
  - Imports and runs the playbook remediateVcenterNonCompliantMeasures.yml to address and remediate any non-compliant security configurations in vCenter.
  - Brings vCenter into alignment with security policies and standards.

- Remediate AD Security Enhancement24

  Tags: HRD_AD, HARDENING

  Purpose:
  - Imports and runs the playbook remediateAdSecurityEnhancement24.yml to enhance the security of Active Directory integration.
  - Applies additional controls and remediations to strengthen AD-related security.

## Automated upgrade scenarios

### First scenario: all-at-once

#### Before you start

1. Check passwords, certificates and backup configuration (if backup works) in sdm001. Upgrade precheck can be executed manually to check if there are mo major errors which can interrupt automated upgrade process.

2. Check backup configuration (if backup works) and opened alarms in NSX.

3. Check backup configuration (if backup works) in vCenter.

4. Check if you can login with `administrator@vsphere.local` to `vcs001` and `vcs002`. It is required for configuration if migration from IWA to LDAP which is a part of automated upgrade process fails.

#### Environment preparation

To prepare environment for code change and ans001 upgrade login to `ans001` on `next` user and execute below commands:

```bash
cd /opt/dhc/update
git pull
git checkout DHC-1.8
```

Ensure that file `upgradeDhc201Prereq.sh` is present in `/opt/dhc/update` and execute this file:

```bash
bash  ./upgradeDhc201Prereq.sh && source /usr/local/bin/py3venv/current/bin/activate
```

#### Automated upgrade run

- All components upgrade in one run:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json"
```

Alternative is to run upgrade by main components in this scenario as below:

- VCF upgrade - management domain in one run:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags 1 --skip-tags 2,3,4,5,6,7
```

- Billing migration in one run:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags 2 --skip-tags 1,3,4,5,6,7
```

- VCF upgrade - workload domain in one run:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags 3 --skip-tags 1,2,4,5,6,7
```

- VCF upgrade - distributed switch in one run:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags 4 --skip-tags 1,2,3,5,6,7
```

- Aria Automation multitenancy enablement in one run:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags 5 --skip-tags 1,2,3,4,6,7
```

- Testing Infra,vRA in one run:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags 6 --skip-tags 1,2,3,4,5,7
```

- Hardening in one run:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags 7 --skip-tags 1,2,3,4,5,6
```

### Second scenario: step-by-step

In this scenario upgrade proces is divided into smaller steps controlled by ansible tags.

#### Before you start

1. Check passwords, certificates and backup configuration (if backup works) in sdm001. Upgrade precheck can be executed manually to check if there are mo major errors which can interrupt automated upgrade process.

2. Check backup configuration (if backup works) and opened alarms in NSX.

3. Check backup configuration (if backup works) in vCenter.

4. Check if you can login with `administrator@vsphere.local` to `vcs001` and `vcs002`. It is required for configuration if migration from IWA to LDAP which is a part of automated upgrade process fails.

#### Environment preparation

To prepare environment for code change and ans001 upgrade login to `ans001` on `next` user and execute below commands:

```bash
cd /opt/dhc/update
git pull
git checkout DHC-1.8
```

Ensure that file `upgradeDhc201Prereq.sh` is present in `/opt/dhc/update` and execute this file:

```bash
bash  ./upgradeDhc201Prereq.sh && source /usr/local/bin/py3venv/current/bin/activate
```

#### Management domain upgrade

- Pre-upgrade preparations and checks + download SDDC Manager upgrade bundle:

  **Customer impact**: NO

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_UPGRADE_PREP,MGT_PRE_UPGRADE_CHECK --skip-tags 2,3,4,5,6,7
```

- SDDC Manager upgrade:

  **Customer impact**: NO but SDDC Manager is not available so it has impact on management itself

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_SDDC_MANAGER_UPGRADE --skip-tags 2,3,4,5,6,7
```

- Remaining upgrade and patching bundles download in SDDC Manager:

  **Customer impact**: NO

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_DOWNLOAD_BUNDLES --skip-tags 2,3,4,5,6,7
```

- Prechecks re-run in SDDC Manager:

  **Customer impact**: NO

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_PRE_UPGRADE_CHECK --skip-tags 2,3,4,5,6,7
```

- NSX upgrade:

  **Customer impact**: YES

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_NSXT_UPGRADE --skip-tags 2,3,4,5,6,7
```

- vCenter upgrade:

  **Customer impact**: YES

  IMPORTANT NOTE: It is required to secure temporary IP address for vCenter upgrade process, by default it is .40 from vCenter network but it can be changed if neccessary by providing variable in extra vars file for example "vcsTempIp": "10.0.0.0".

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_VCS_UPGRADE --skip-tags 2,3,4,5,6,7
```

- ESXi upgrade:

  **Customer impact**: YES
  
  IMPORTANT NOTE: Avamar proxy is turned off during upgrade process.

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_ESXI_UPGRADE --skip-tags 2,3,4,5,6,7
```

- VCF re-licensing and config drift installation:

  **Customer impact**: NO
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_POST_UPGRADE --skip-tags 2,3,4,5,6,7
```

- Patching prechecks and preparations:

  **Customer impact**: NO
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_PATCHING_PREP --skip-tags 2,3,4,5,6,7
```

- NSX patching:

  **Customer impact**: YES
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_NSXT_PATCHING --skip-tags 2,3,4,5,6,7
```

- vCenter patching:

  **Customer impact**: YES
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_VCS_PATCHING --skip-tags 2,3,4,5,6,7
```

- ESXi patching:

  **Customer impact**: YES
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_ESXI_PATCHING --skip-tags 2,3,4,5,6,7
```

- VSAN ondisk format upgrade and config drift installation:

  **Customer impact**: NO
  
  IMPORTANT NOTE: It will not execute if version is 3 or less because it requires all esxi host evacuation in such case.

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_POST_PATCHING --skip-tags 2,3,4,5,6,7
```

- VRA tagging adjustements:

  **Customer impact**: YES
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_NSXT_TAGGING_DISABLE --skip-tags 2,3,4,5,6,7
```

- Witness host upgrade:

  **Customer impact**: YES

  IMPORTANT NOTE: During this process maintenance mode will NOT be enabled for any of objects.
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags MGT_WITNESS_UPGRADE --skip-tags 2,3,4,5,6,7
```

#### Billing migration

- Check SCP billing status on bil001:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags BIL_SCP_HEALTH_BIL001 --skip-tags 1,3,4,5,6,7
```

- Migrate billing from SCP to GCP on bil001:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags BIL_ANS_MIGRATE_GCP --skip-tags 1,3,4,5,6,7
```

- Check GCP billing status on bil001:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags BIL_GCP_HEALTH_BIL001 --skip-tags 1,3,4,5,6,7
```

- Prepare ans001 for billing functionality:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags BIL_ANS_MIGRATE_PREP --skip-tags 1,3,4,5,6,7
```

- Copy files from bil001 to ans001:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags BIL_ANS_MIGRATE_FILES --skip-tags 1,3,4,5,6,7
```

- Final tasks done on ans001:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags BIL_ANS_MIGRATE_FINAL --skip-tags 1,3,4,5,6,7
```

- NSX DFW changes for billing:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags BIL_ANS_MIGRATE_DFW --skip-tags 1,3,4,5,6,7
```

- Check GCP billing status on ans001:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags BIL_GCP_HEALTH_ANS001 --skip-tags 1,3,4,5,6,7
```

#### VCF upgrade - workload domain

- Pre-upgrade preparations and checks, download bundles, preparing upgrade plan in SDDC Manager:

  **Customer impact**: NO

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_UPGRADE_PREP --skip-tags 1,2,4,5,6,7
```

- NSX upgrade:

  **Customer impact**: YES

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_NSXT_UPGRADE --skip-tags 1,2,4,5,6,7
```

- vCenter upgrade:

  **Customer impact**: YES

  IMPORTANT NOTE: It is required to secure temporary IP address for vCenter upgrade process, by default it is .40 from vCenter network but it can be changed if neccessary by providing variable in extra vars file for example "vcsTempIp": "10.0.0.0".

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_VCS_UPGRADE --skip-tags 1,2,4,5,6,7
```

- ESXi upgrade:

  **Customer impact**: YES
  
  IMPORTANT NOTE: Avamar proxy is turned off during upgrade process.

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_ESXI_UPGRADE --skip-tags 1,2,4,5,6,7
```

- VCF re-licensing, config drift installation and VSAN upgrade:

  **Customer impact**: NO
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_POST_UPGRADE --skip-tags 1,2,4,5,6,7
```

- Patching prechecks and preparations:

  **Customer impact**: NO
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_PATCHING_PREP --skip-tags 1,2,4,5,6,7
```

- NSX patching:

  **Customer impact**: YES
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_NSXT_PATCHING --skip-tags 1,2,4,5,6,7
```

- vCenter patching:

  **Customer impact**: YES
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_VCS_PATCHING --skip-tags 1,2,4,5,6,7
```

- ESXi patching:

  **Customer impact**: YES
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_ESXI_PATCHING --skip-tags 1,2,4,5,6,7
```

- VSAN ondisk format upgrade and config drift installation:

  **Customer impact**: NO
  
  IMPORTANT NOTE: It will not execute if version is 3 or less because it requires all esxi host evacuation in such case.

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_POST_PATCHING --skip-tags 1,2,4,5,6,7
```

- Witness host upgrade:

  **Customer impact**: YES

  IMPORTANT NOTE: During this process maintenance mode will NOT be enabled for any of objects.
  
```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VI_WITNESS_UPGRADE --skip-tags 1,2,4,5,6,7
```

#### VCF upgrade - distributed switch

- Upgrade of distributed switches:

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags 4 --skip-tags 1,2,3,5,6,7
```

#### Aria Automation multitenancy enablement

- Aria Automation nodes resize:

  **Customer impact**: YES

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VRA_RESIZE --skip-tags 1,2,3,4,6,7
```

- Aria Automation multitenancy enablement:

  **Customer impact**: YES

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VRA_CONFIGURE_MULTITENANCY --skip-tags 1,2,3,4,6,7
```

#### Testing Infra,vRA

- Testing Infra:

  **Customer impact**: NO

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags INFRA_TESTING --skip-tags 1,2,3,4,5,7
```

- Testing vRA Catalog Item:

  **Customer impact**: NO

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VRA_CATALOG_TESTING --skip-tags 1,2,3,4,5,7
```

- Testing vRA Day2Action:

  **Customer impact**: NO

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags VRA_DAY2_TESTING --skip-tags 1,2,3,4,5,7
```

#### Hardening

- Hardening ESXi8:

  **Customer impact**: NO

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags HRD_ESXI --skip-tags 1,2,3,4,5,6
```

- Remediate VCenter Non-Compliant Measures:

  **Customer impact**: NO

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags HRD_VCENTER --skip-tags 1,2,3,4,5,6
```

- Remediate AD Security Enhancement24:

  **Customer impact**: NO

```bash
ansible-playbook upgradeDhc201Main.yml -e "@./vars.json" --tags HRD_AD --skip-tags 1,2,3,4,5,6
```
