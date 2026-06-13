# Remove Antivirus Relay

## Table of contents

## Changelog

|    Date    |   TOS   |   Issue   | Author | Description |
|------------|---------|-----------|--------|-------------|
| 17.10.2023 | VCS 1.8 | VCS-10997 | Kacper Kuliberda | Initial instruction creation |

## Introduction

### Purpose

Remove Antivirus Relay VM and related configuration from existing VCS deployments

### Audience

- VCS Engineers
- VCS Operations

### Scope

This instruction covers configuring the following items:

- Remove Antivirus Relay virtual machine
- Remove AV Relay configuration
  - HashiCorp Vault Entries
  - Firewall Configuration

## Remove AVR configuration

### NSX Firewall

1. Login to NSX UI
2. Go to Inventory -> Groups
3. Note the members and 'where used' of these security groups:
   1. seg017
   2. seg017_APPLYTO
   3. seg018
   4. seg018_APPLYTO
4. Unassign these security groups from all firewall rules they appear in
5. Remove the security groups

### Remove the Vault Entry

Navigate to the `update` directory, execute the 'removeAvFromVault.yml' playbook.

## Remove the Virtual Machine

1. Login to vCenter UI
2. Remove the avr001 virtual machine

## Remove AVR from hosts and group_vars

Repeat the following steps for `group_vars/all` and `hosts` files in all config locations (update, manage, deploy):

1. If present, remove all mentions of `avr001` VM:
   1. IP definitions
   2. Mentions in patching groups etc.
2. If present, remove all mentions of `avr001` VM:
   1. IP definition in mgmtDns section
