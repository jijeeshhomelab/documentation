# Table of Contents

- [Table of Contents](#table-of-contents)
- [Title: Lifecycle Management - 1.8.1](#title-lifecycle-management---181)
- [List of Changes](#list-of-changes)
- [Introduction](#introduction)
- [Scope](#scope)
- [Related Documents](#related-documents)
- [Upgrade Steps](#upgrade-steps)
  - [LCM code update (Clone Repository)](#lcm-code-update-clone-repository)
  - [non-VCF components](#non-vcf-components)
    - [Update proxy whitelist](#update-proxy-whitelist)
    - [Update DSA entries on HashiCorp Vault](#update-dsa-entries-on-hashicorp-vault)
    - [Create DNS zones for Deep Security domain](#create-dns-zones-for-deep-security-domain)
    - [Download Binaries](#download-binaries)
    - [Ansible upgrade steps](#ansible-upgrade-steps)
      - [Pre-Upgrade Steps](#pre-upgrade-steps)
      - [Upgrading Ubuntu](#upgrading-ubuntu)
        - [Upgrade to Ubuntu 20.04](#upgrade-to-ubuntu-2004)
        - [Upgrade to Ubuntu 22.04](#upgrade-to-ubuntu-2204)
      - [Post-Upgrade Steps](#post-upgrade-steps)
    - [Add Ubuntu 22.04 LTS template password to Vault](#add-ubuntu-2204-lts-template-password-to-vault)
    - [Import Ubuntu 22.04 LTS template](#import-ubuntu-2204-lts-template)
    - [Recreate Nessus Server part 1](#recreate-nessus-server-part-1)
    - [Recreate HashiVault](#recreate-hashivault)
    - [Recreate Squid Proxy](#recreate-squid-proxy)
    - [Recreate Deb Server](#recreate-deb-server)
    - [Recreate SMTP Relay Server](#recreate-smtp-relay-server)
    - [Recreate Http Gateway](#recreate-http-gateway)
    - [Recreate Mid Server](#recreate-mid-server)
    - [Recreate Billing Server](#recreate-billing-server)
    - [Recreate Nessus Server part 2](#recreate-nessus-server-part-2)
    - [Remove GitLab Server](#remove-gitlab-server)
      - [Remove the VM, AD and DNS configuration](#remove-the-vm-ad-and-dns-configuration)
      - [Remove NSX Firewall configuration](#remove-nsx-firewall-configuration)
      - [Remove Active Directory Resource Group and Service Account](#remove-active-directory-resource-group-and-service-account)
      - [Remove GIT from hosts and group\_vars](#remove-git-from-hosts-and-group_vars)
    - [Remove Antivirus Relay VM](#remove-antivirus-relay-vm)
      - [Remove NSX Firewall groups](#remove-nsx-firewall-groups)
      - [Remove the Vault Entry](#remove-the-vault-entry)
      - [Remove the Virtual Machine](#remove-the-virtual-machine)
      - [Remove AVR from hosts and group\_vars](#remove-avr-from-hosts-and-group_vars)
- [Patching Linux VMs](#patching-linux-vms)
- [Process monitoring](#process-monitoring)
- [Git repository checkout](#git-repository-checkout)
- [Rollback](#rollback)

# Title: Lifecycle Management - 1.8.1

# List of Changes

| Date       | Issue    | Author          | TOS  | Description |
| ---------- | -------- | --------------- | ---- | ---------------------- |
| 28/11/2023 | VCS-10995 | Mariusz Stanek |      | Initial draft creation |
| 11/01/2024 | VCS-11824 | Lukasz Tomaszewski |      | Minor updates |
| 09/04/2024 | VCS-12332 | Lukasz Tomaszewski |      | Updates |

# Introduction

This page describes Life Cycle Management of VCS components. Some VCS components can be upgraded independently, others have to follow the exact order.

# Scope

The work instruction is intended to cover below tasks:

- LCM code update.
- Upgrade of non-VCF components
- Post LCM validation

# Related Documents

| Document |
| -------- |
| [VCS 1.8 - wiLifeCycleManagement](wiLifeCycleManagement-DHC1.8.md) |

# Upgrade Steps

The upgrade steps contain both manual and automated (if feasible) parts.

**Before an upgrade, ensure:**

- Maintenance plan is agreed and approved, it is in-line with LCM process.
- It is expected the upgrade is performed by a person(s) with expert knowledge in VMware, Linux and VCS solution. Engineers must have sufficient privileges.
- Image backups are created and available.
- All accounts are valid (not locked due, for example expired password).
- The playbooks mentioned in this work instruction, unless otherwise specified (for example user *next*), are executed from user home directory */home/axxxxx/dhc/update*, by an engineer logged in with their dedicated domain account.
- Port 464 (Ketberos, TCP and UDP) must be enabled on NSX-T Distributed Fireall in group ToAD.
- Collect DSA (DeepSecurity Agent - Trend Micro AntiVirus) information for: tenantId, token, linuxPolicyId and windowsPolicyId. It can be found on ans002 group_vars/all or from implementation project excel sheet.
- DSA - in case of issues with reactivating AV agent, please contact with BDS antivirus team: <dl-ro-bds-trendmicro@eviden.com> (when no response+ <gabriel.popa@eviden.com> for escalation).
- Note: if dsa installation fails it can be run later separately by running (bil001 as an example):

    ```bash
    ansible-playbook installAvAgent.yml -e HOSTS=bil001
    ```

The majority of upgrade tasks should take place in order, defined by below paragraphs.

>Note: All the playbooks run in the update and manage phase will require credentials from VCS management domain

![authentication](images/wiLifeCycleManagement/auth1.PNG)

## LCM code update (Clone Repository)

Please use following steps to clone VCS repository. Execute all commands as *domain user* logged on Ansible Host. Change:

```bash
axxxxx@ans001:~$ cd
```

Configure git and clone VCS repository:

```bash
git config --global user.name 'User Name' && git config --global user.email 'email.address@atos.net'
git config --global core.hookspath 'hooks' && git config --global credential.helper store && git config --global submodule.recurse true
git config --global http.proxy 'http://< locationCode >pxy001.< customerCode >dhc01.next:3128'
git clone https://github.com/GLB-CES-PrivateCloud/dhc.git --recurse-submodules
git pull
```

Move to */home/axxxxx/dhc/version-matrix* directory and and switch branch to *DHC-1.8.1*:

```bash
cd /home/axxxxx/dhc/version-matrix

git checkout DHC-1.8.1
```

Move to */home/axxxxx/dhc/update* directory and and switch branch to *DHC-1.8.1*:

```bash
cd /home/axxxxx/dhc/update

git checkout DHC-1.8.1
```

## non-VCF components

LCM VCS 1.8.1 proces is related with upgrade from Ubuntu 18 to Ubuntu 22. Proceed with steps in order described below.  

### Update proxy whitelist

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook updateProxyWhitelist.yml
```

### Update DSA entries on HashiCorp Vault

Collect DSA (DeepSecurity AntiVirus) information for: tenantId, token, linuxPolicyId and windowsPolicyId. It can be found on ans002 group_vars/all or from implementation project excel sheet.

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook addDeepSecurityToVault.yml
```

### Create DNS zones for Deep Security domain

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook createDeepSecurityDnsZone.yml
```

### Download Binaries

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook downloadBinaries.yml
```

### Ansible upgrade steps

Execute all below mentioned playbooks on *ans001* server from */home/axxxxx/dhc/update* folder.

#### Pre-Upgrade Steps

Execute the following Ansible playbook for pre-upgrade preparations(snapshot, package list, proxy settings, python download).

```bash
ansible-playbook ansPreUpgrade.yml
```

IMPORTANT NOTE: Make sure the playbook finished successfully before moving on to the next step.

#### Upgrading Ubuntu

##### Upgrade to Ubuntu 20.04

After successfully completing the pre-upgrade steps, as user *next* execute the following commands in the given sequence to upgrade to Ubuntu 20:

```bash
sudo apt update -y
sudo DEBIAN_FRONTEND=noninteractive apt upgrade -y
```

After upgrade of packages please reboot the system, and after a reboot please execute:

```bash
SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt sudo do-release-upgrade -f DistUpgradeViewNonInteractive
```

The process of upgrade to Ubuntu 20 begins. When it is finished please reboot machine again
After rebooting, your Ubuntu version should be updated to 20.04.

##### Upgrade to Ubuntu 22.04

To proceed to Ubuntu 22.04, execute the same set of commands as mentioned in the previous [Upgrade to Ubuntu 20.04](#upgrade-to-ubuntu-2004) section.

#### Post-Upgrade Steps

Once the system is upgraded and rebooted, do following as a user *next*.

- Run the following bash script to rebuild the virtual environment and python, script should be located in the user home directory:

    ```bash
    ./rebuildPython.sh
    ```

- Execute the post-upgrade Ansible playbook:

    ```bash
    ansible-playbook ansPostUpgrade.yml
    ```

### Add Ubuntu 22.04 LTS template password to Vault

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook addTemplatePasswordToVault.yml
```

### Import Ubuntu 22.04 LTS template

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook importTemplates.yml
```

### Recreate Nessus Server part 1

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook recreateNes.yml --skip-tags "createScan,scheduleScan,installAgents"
```

Intentional split into 2 parts as:

Known issue: once the license is applied to Nessus it starts to compile plugins.
During this time Nessus has limited functionality (ie. it doesn't allow to create scans).
Development team noticed 2 different behaviours:

- Nessus allows to open "Create new scan" webpage, but Save button is inactive or Nessus returns "400 Bad request" error.
- In the first example selenium script which creates new scans is able to monitor the state of the button, and it waits once the button is active to complete its operations.
- In the second example ansible playbook will fail.

### Recreate HashiVault

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook recreateHsv.yml
```

### Recreate Squid Proxy

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook recreatePxy.yml
```

### Recreate Deb Server

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook recreateDeb.yml
```

IMPORTANT NOTE: Deb server will create repositories during first night after deploy so it will be fully operational on next day.

### Recreate SMTP Relay Server

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook recreateSrs.yml
```

### Recreate Http Gateway

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook recreateHgw.yml
```

### Recreate Mid Server

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook recreateMid.yml
```

### Recreate Billing Server

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook recreateBil.yml
```

### Recreate Nessus Server part 2

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook recreateNes.yml --tags "createScan,scheduleScan,installAgents"
```

### Remove GitLab Server

Below instruction (based on [wiRemoveGIT.md](wiRemoveGIT.md)) covers configuring the following items:

- Remove GitLab virtual machine
- Remove GitLab configuration
  - HashiCorp Vault Entries
  - Firewall Configuration
  - DNS configuration
  - Active Directory Configuration
  - Ansible Configuration

#### Remove the VM, AD and DNS configuration

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook removeGitLab.yml
```

The playbook removes vault entries, deletes the VM, removes the VM from AD and DNS configuration.

#### Remove NSX Firewall configuration

- These security groups are related with GitLab:
  - < customerCode >seg033_APPLYTO
  - < customerCode >seg033
- Run the `removeNsxInventoryGroup.yml` (*/home/axxxxx/dhc/update*) playbook twice, once for every group to remove them.

```bash
ansible-playbook removeNsxInventoryGroup.yml
```

> NOTE: This playbook can only unassign the target group from rule source and destination if the target group is not the only member of a rule. That is, the playbook will fail if removing the group would result in 'any source to any destination' firewall rule. Run the playbook, if it errors out, the problematic rule will be listed in the `url` key. Remove the rule manually and re-run the playbook.

#### Remove Active Directory Resource Group and Service Account

- Login to Active Directory controller (open Active Directory Users and Computers)
- Remove the `rsce-< locationCode >-git-l-admins` resource group
- Remove the `svc-< locationCode >-git01` service account

#### Remove GIT from hosts and group_vars

Repeat the following steps for `group_vars/all` and `hosts` files in all config locations under */opt/dhc* (update, manage, deploy) and at least */home/axxxxx/dhc/update*:

- If present, remove all mentions of `git001` VM in the `hosts` file:
  - Host definitions
  - Mentions in patching groups, host groups etc.
- If present, remove all mentions of `git001` VM in `group_vars`:
  - IP definition in mgmtDns section

### Remove Antivirus Relay VM

Below instruction (based on [wiRemoveAVR.md](wiRemoveAVR.md)) covers configuring the following items:

- Remove Antivirus Relay virtual machine
- Remove AV Relay configuration
  - HashiCorp Vault Entries
  - Firewall Configuration

#### Remove NSX Firewall groups

- These security groups are related with Antivirus Relay VM:
  - < customerCode >seg017
  - < customerCode >seg017_APPLYTO
  - < customerCode >seg018
  - < customerCode >seg018_APPLYTO
- Run the `removeNsxInventoryGroup.yml` (*/home/axxxxx/dhc/update*) playbook 4 times, once for every group to remove them.

```bash
ansible-playbook removeNsxInventoryGroup.yml
```

> NOTE: This playbook can only unassign the target group from rule source and destination if the target group is not the only member of a rule. That is, the playbook will fail if removing the group would result in 'any source to any destination' firewall rule. Run the playbook, if it errors out, the problematic rule will be listed in the `url` key. Remove the rule manually and re-run the playbook.

#### Remove the Vault Entry

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook removeAvFromVault.yml
```

#### Remove the Virtual Machine

- Login to vCenter UI
- Remove the avr001 virtual machine

#### Remove AVR from hosts and group_vars

Repeat the following steps for `group_vars/all` and `hosts` files in all config locations under */opt/dhc* (update, manage, deploy) and at least */home/axxxxx/dhc/update*:

- If present, remove all mentions of `avr001` VM:
  - IP definitions
  - Mentions in patching groups etc.
- If present, remove all mentions of `avr001` VM:
  - IP definition in mgmtDns section

# Patching Linux VMs

Once all Ubuntu servers are recreated and all changes to the inventory (ie. patching groups) are made, move to /home/axxxxx/dhc/manage directory and switch branch to DHC-1.8.1:

```bash
cd /home/axxxxx/dhc/manage
git checkout DHC-1.8.1
```

Please execute linux patching based on below WI but running the playbook with var: " source=default " (this is because DEB server repositories are not immediately available since synchronization takes almost 2 days and source=default will do the patching from online sources )
example:

```bash
cd /home/axxxxx/dhc/update
ansible-playbook patchLinux.yml -e "HOSTS=MMgroupL1 source=default"
ansible-playbook patchLinux.yml -e "HOSTS=MMgroupL2 source=default"
ansible-playbook patchLinux.yml -e "HOSTS=MMgroupL3 source=default"
ansible-playbook patchLinux.yml -e "HOSTS=MMgroupL4 source=default"
```

[Linux Patching](linuxPatching.md)

# Process monitoring

For all recreated Ubuntu VMs it is required to re-enable process monitoring. In order to execute this, follow [this work instruction](wiReplaceVropsEpopsAgentWithTelegrafAgent.md), specifically the 'Enable custom process monitoring on Linux management VMs' step.

# Git repository checkout

Once all Ubuntu servers are recreated and all changes to the inventory (ie. patching groups) are made, move to /opt/dhc/manage directory and switch branch to the appropriate VCS 1.8.1 tag using `manageDhcRepository.yml`

# Rollback

Ansible VM (*ans001*) snapshot is being done during *ansPreUpgrade.yml* execution.

Other Linux VMs (Ubuntu 18) are cloned during execution of recreation scripts. For example Ubuntu 18 *bil001* is cloned to *bil001_clone*. After that Ubuntu 22 *bil001* is deployed and turned on. It gives a possibility to rollback.

Rollback activities have to be done manually as follows:

- for *ans001* from vCenter Snapshots,
- for other Ubuntu 22 machines:
  - remove particular Ubuntu 22 server from vCenter (for example *bil001*),
  - clone VM (for example *bil001_clone* Ubuntu 18 to *bil001*),
  - start new cloned Ubuntu 18 *bil001* VM.
