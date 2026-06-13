# Using VCS Code in Production Environments

## Table of contents

- [Using VCS Code in Production Environments](#using-vcs-code-in-production-environments)
  - [Table of contents](#table-of-contents)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Prerequisites](#prerequisites)
  - [VCS Repository and Subrepositories](#vcs-repository-and-subrepositories)
  - [Checking the Code Version](#checking-the-code-version)
    - [Updating the repository](#updating-the-repository)
    - [Updating the repository - process](#updating-the-repository---process)
  - [Playbook Execution](#playbook-execution)

## Changelog

|    Date    |   TOS   |   Issue   | Author | Description |
|------------|---------|-----------|--------|-------------|
| 27.07.2022 | VCS 1.6 | CESDHC-556 | Kacper Kuliberda | Initial document creation |
| 12.10.2023 | VCS 1.8 | VCS-10412 | Kacper Kuliberda | Update document with new code upgrade process|

## Introduction

### Purpose

Explain the usage of VCS Code in a production environment, decide when and how the code should be updated.

### Audience

- VCS Operations

### Scope

- Checking the version of VCS Code
- Deciding when to update the VCS Code
- Executing VCS Code

## Prerequisites

- Access to the desired production environment CoreVM (ans001)

## VCS Repository and Subrepositories

The VCS codebase is comprised of the 'main' VCS repository, in which several directories are contained. Some of these directories are repositories in themselves, further referred to as 'subrepositories' or 'subrepos'.

- deploy - mapped to the DHC-Deploy repository on Github - used ONLY in deployment, should be ignored after the environment is hardened and turned over to production
- manage - mapped to the DHC-Manage repository - used for operational playbooks or external solution integration tasks
- update - mapped to the DHC-Update repository - used ONLY during LCM (platform upgrade or patching), should be ignored otherwise
- firewall - contains VCS firewall configuration files
- version-matrix - contains a JSON file listing the platform component versions for a given VCS version

In production use, the main focus is on the version of the main VCS repository.

## Checking the Code Version

Prior to executing any playbooks, make sure the code matches the installed VCS Version.

- View the platform configuration file in VCS's group_vars
- Note the value of the 'componentCurrentVersion' variable. This value can be used alongside the VCS version matrix to check which component versions should be installed. The details of this check are out of scope for this work instruction.
- Navigate to /opt/dhc and run the following command:

   ```shell
   git status
   ```

- Note the output, it should look something like this:

   ```text
   HEAD detached at DHC-1.9.0-20231231
   ```

- If the output mentions modified files or does not look like a standard [VCS release tag](https://github.com/GLB-CES-PrivateCloud/DHC/releases) you may need to refresh the code.

### Updating the repository

The componentCurrentVersion variable has a slightly different format than the release tags:

- dhcVersion1_6_1 corresponds to DHC-1.6.1-YYYYMMDD
- dhcVersion1_10_4 corresponds to DHC-1.10.4-YYYYMMDD etc.
- Depending on the output of `git status`, you can take the following actions:
  1. If the `Changes not staged for commit (...) modified: <subrepo>` section is **not** present **AND** the `HEAD detached` line shows a tag that matches with the previously checked 'componentCurrentVersion', **AND** the tag date is the last available fix release for this version, **no action required**.
  2. If the `Changes not staged for commit (...) modified: <subrepo>` section is **not** present **AND** the `HEAD detached` line shows a tag that matches with the previously checked 'componentCurrentVersion', **BUT** the tag date is **NOT** the last available fix release for this version, **you may want to update the code**.
  3. If the `Changes not staged for commit (...) modified: <subrepo>` section is present, **you may need to refresh the code** unless you can confirm the changes have been approved. Regardless, these changes will be lost once the code is updated to the next version, so ensure engineering applies the fix (if the in-place code change was related to a bug).
  4. If the output shows a branch that does not match the standard [VCS release tag](https://github.com/GLB-CES-PrivateCloud/DHC/releases) format, you will need to update the repository.

### Updating the repository - process

The VCS Repository can be updated by using the 'manageDhcRepository.yml' playbook.

In order to properly execute the playbook you will need to create a [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) and authenticate it against Atos SSO. Additionally, visit [the releases page](https://github.com/GLB-CES-PrivateCloud/DHC/releases) and choose a VCS tag which will be used to upgrade the code.

Follow the playbook prompts. After a successful code update, you can proceed with playbook execution.

## Playbook Execution

In order to execute the playbooks, first it is necessary to have a domain account in the desired environment (see the prerequisites section). If you can access ans001/CoreVM, you already have a domain account.

The playbooks start with a commented section containing information about the playbook execution, required run parameters and a short description. This section may also contain the name of a linked work instruction to be found in the VCS Documentation repository.

Furthermore, if you're looking for a playbook with a particular functionality, a list of all playbook descriptions can be found in the [Operational Playbooks](operationalPlaybooks.md) document.

All playbooks should be executed as a domain user (`aXXXXXX@domaindhc.next`) from the /opt/dhc/XXX directory. For this reason, most of the playbooks ask for the domain credentials of the executing user.  
Upon receiving these credentials, they are usually used to access HashiVault, from which another set of credentials is sourced, this time for the proper service account required by the playbook. The explanation of this mechanism is out of scope for this document. See [LLD VCS Role Based Access Control](../design/lldDhcRoleBasedAccessControl.md).
