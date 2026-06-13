# Hardening of vCenter8 following Atos TSS document

## Table of Contents

- [Hardening of vCenter8 following Atos TSS document](#hardening-of-vcenter8-following-atos-tss-document)
  - [Table of Contents](#table-of-contents)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Pre-Requisite](#pre-requisite)
  - [Scope](#scope)
  - [Documentation](#documentation)
  - [Usage](#usage)

## Changelog
  
 |    Date    |  TOS   | Issue   | Author | Description |
 |------------|---------|-----------|--------|--------|
 | 07.07.2024 |  VCS 2.0   |    VCS-13188    | Aswin Arumugam M | Initial draft creation |
 | 18.07.2024 |  VCS 2.0   |    VCS-13381    | Aswin Arumugam M | Document update with Certificate authentication |
 | 30.07.2024 |  VCS 2.0   |    VCS-13476    | Aswin Arumugam M | Document update with dependency list |

## Introduction

This playbook is designed to configure advanced system settings on vCenter8 Appliance based on predefined values and fix any vulnerabilities identified according to Atos TSS documentations. It also ensures the proper management of vCenter8 Root and SSO password policy and also sets the standards for Switches created.

### Purpose

Modify vCenter8 Appliance advanced settings, acceptance level on the switch created according to Atos TSS Document. We are also remediating the SSO and ROOT policy settings for vCenter8.

### Audience

- Greenfield and DevSecOps Team.

### Pre-Requisite

For a new VCS environment these would be installed in the server during Ansible build and configuration.
We would need the below pre-requisite:

1. Automation 04 account present in Vault
2. Ansible - 2.15
3. Python - 3.10
4. PowerShell - 7.2.11
5. Aiohttp - 3.9.5  

## Scope

1. Modify VMware vSphere Distributed switch(VDS) settings:
  a) For rejecting the ‘Forged Transmits’.
  b) For rejecting the ‘Media Access Control (MAC)’ address
  c) For rejecting the ‘Promiscuous Mode’ Policy
  d) Restricting the Port-Level Configuration Overrides
2. By default TLS v1.0 and 1.1 is not supported in vCenter8.
3. Modify the Root account policy to comply with the Atos policy.
4. Modify the SSO vsphere.local password policy to comply with the Atos policy.

## Documentation

[Atos TSS Documents](https://atos365.sharepoint.com/:b:/r/sites/100000120/PublishedStorage/Technical%20Security%20Specifications%20for%20VMware%20VCenter%208.0.pdf?csf=1&web=1&e=ycxCW8)

## Usage

1. Clone the repository containing this playbook. This is currently placed in DHC-2.0 Deploy and Manage branch.
2. Run the playbook using the below command.
3. There will be HTML report stored in the "/tmp" folder in ansible server showing the changes done in the vCenter appliance setting.
4. For the Root policy the output will be shown in the terminal.

EMAILID --> Specify the email ID to which the password expiration email has to be sent. It is set as a variable in the playbook.

```ansible
(ans420-std) dhc/manage$ ansible-playbook remediateVcenterNonCompliantMeasures.yml 
```

If you want to run a specific remediation you can use the Tags specified in playbook.

If you want to run a specific remediation you can use the Tags specified in playbook.

 |    TAG Name   |  Description  |
 |------------|---------|
 | NETPOLICY |  Runs all the remediation except Root policy |
 | 1VV00001 |  Runs remediation for rejecting Forged Transmits    |
 | 1VV00002 |  Runs remediation for rejecting Media Access Control (MAC)  |
 | 1VV00003 |  Runs remediation for rejecting Promiscuous Mode  |
 | 1VV00004 |  Runs remediation for rejecting Port-Level Configuration Overrides On VDS  |
 | SSO |  Runs remediation for updating vsphere.local password policy   |
 | ROOT |  Runs remediation for updating root account policy  |

**NOTE:** We have used certificate of service account (automation user 04) in the playbook to connect to Hashi vault. We do not need AD domain ID creds to be passed to the play.
