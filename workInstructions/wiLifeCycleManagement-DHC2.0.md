# Title: Lifecycle Management - 2.0

# List of Changes

| Date       | Issue    | Author          | TOS  | Description |
| ---------- | -------- | --------------- | ---- | ---------------------- |
| 24/07/2024 | VCS-13373 | Łukasz Tomaszewski |      | Initial version |

# Introduction

This page describes Life Cycle Management of DHC components. Some DHC components can be upgraded independently, others have to follow the exact order.

# Scope

The work instruction is intended to cover below tasks:

- LCM code adaptation and update
- non-VCF components upgrade
- VCF components upgrade
- Post LCM validation

# Related Documents

| Document |
| -------- |
| |

# Upgrade Steps

The upgrade steps contain both manual and automated (if feasible) parts.

**Before an upgrade, ensure:**

- Maintenance plan is agreed and approved, it is in-line with LCM process.
- It is expected the upgrade is performed by a person(s) with expert knowledge in VMware, Linux and DHC solution. Engineers must have sufficient privileges.
- Image backups are created and available.
- All accounts are valid (not locked due, for example expired password).
- The playbooks mentioned in this work instruction, unless otherwise specified (for example user *next*), are executed by an engineer logged in with their dedicated domain account.

The majority of upgrade tasks should take place in order, defined by below paragraphs.

>Note: All the playbooks run in the update and manage phase will require credentials from DHC management domain

![authentication](images/wiLifeCycleManagement/auth1.PNG)

## LCM code adaptation and update

### Move ansible group_vars and inventory (ans001)

Fetch and download the latest content of update repository. Execute below as a user *next*:

```bash
sudo su next
cd /opt/dhc/update
git pull
git checkout DHC-1.8
```

Next, execute the following playbook from */opt/dhc/update* folder.

```bash
ansible-playbook createDhcDonfig.yml
```

### Code update (ans001)

---
To upgrade the code execute the playbook on *ans001* server from */opt/dhc/manage/* directory:

```bash
ansible-playbook manageDhcRepository.yml
```

The `manageDhcRepository.yml` playbook is available from version `DHC-1.5-latest` and later.

Familiarize yourself with the playbook description and arrange pre-requisites:

- Internet connection (at least to github.com) is required.
- Account on *github.com* with at least a read-only access to the DHC repositories is required.
- A GitHub access token with at least read privileges is required.

The playbook will prompt the user to input a release tag to upgrade the code to. The tags can be found at <https://github.com/GLB-CES-PrivateCloud/DHC/tags>. For a given DHC version, i.e. DHC 2.0, the latest available tag for that version should be chosen.  
Example, the available tags are `DHC-2.0-20240101` and `DHC-2.0-20240301`. The last part is a release date in YYYYMMDD format, therefore the later one should be preferred.

>Note, **the first run will fail by design**, as the playbook backs up the existing code as a first step. **You will be prompted to execute this playbook from a backup location.**
>
>By following the prompts you should end up with code updated to the desired release.

New code upgrade process updates the version Matrix file which is stored in *`/opt/dhc/version-matrix/versionMatrix.json`*. This is default location for both *manage* and *update* playbooks.

## non-VCF components

DHC 2.0 LCM process contains:

- upgrade ansible python virtual environment
- update binaries

Proceed with steps in order described below.  

### Upgrade ansible python virtual environment

Execute the following playbook on *ans001* server from */opt/dhc/update* folder.

```bash
ansible-playbook upgradeAnsible.yml
```

### Download Binaries

Execute the following playbook on *ans001* server from */opt/dhc/update* folder.

```bash
ansible-playbook downloadBinaries.yml
```

## VCF components

## Post LCM validation
