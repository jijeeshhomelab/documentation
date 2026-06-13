# Remove GitLab Server

## Table of contents

## Changelog

|    Date    |   TOS   |   Issue   | Author | Description |
|------------|---------|-----------|--------|-------------|
| 09.11.2023 | VCS 1.8.1 | VCS-11412 | Kacper Kuliberda | Initial instruction creation |

## Introduction

### Purpose

Remove GitLab VM and related configuration from existing VCS deployments

### Audience

- VCS Engineers
- VCS Operations

### Scope

This instruction covers configuring the following items:

- Remove GitLab virtual machine
- Remove GitLab configuration
  - HashiCorp Vault Entries
  - Firewall Configuration
  - DNS configuration
  - Active Directory Configuration
  - Ansible Configuration

## Remove the VM, AD and DNS configuration

Navigate to the `update` directory, execute the `removeGitLab.yml` playbook.  
The playbook removes vault entries, deletes the VM, removes the VM from AD and DNS configuration.

## Remove other configuration

### NSX Firewall

1. Login to NSX UI
2. Export FW configuration
3. Go to Inventory -> Groups
4. Note the names, members and 'where used' of these security groups:
   1. < locationCode >seg033_APPLYTO
   2. < locationCode >seg033
5. Run the `removeNsxInventoryGroup.yml` helper playbook twice, once for every group
6. Remove the 'GIT' policy from DFW configuration

Alternatively, you may unassign and remove the groups manually.

### Active Directory Resource Group and Service Account

1. Login to Active Directory controller
2. Remove the `rsce-< locationCode >-git-l-admins` resource group
3. Remove the `svc-< locationCode >-git01` service account

### Remove GIT from hosts and group_vars

Repeat the following steps for `group_vars/all` and `hosts` files in all config locations (update, manage, deploy):

1. If present, remove all mentions of `git001` VM in the `hosts` file:
   1. Host definitions
   2. Mentions in patching groups, host groups etc.
2. If present, remove all mentions of `git001` VM in `group_vars`:
   1. IP definition in mgmtDns section
