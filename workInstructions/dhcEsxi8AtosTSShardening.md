# Hardening of ESXi8 following Atos TSS document

## Table of Contents

- [Hardening of ESXi8 following Atos TSS document](#hardening-of-esxi8-following-atos-tss-document)
  - [Table of Contents](#table-of-contents)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
  - [Scope](#scope)
  - [Documentation](#documentation)
  - [Usage](#usage)

## Changelog
  
 |    Date    |  TOS   | Issue   | Author | Description |
 |------------|---------|-----------|--------|--------|
 | 04.25.2024 |  VCS 2.0   |    VCS-10586    | Adrian Giurgiu | Initial draft creation |

## Introduction

This playbook is designed to configure advanced system settings on ESXi hosts based on predefined values and fix any vulnerabilities identified according to Atos TSS documentations. It also ensures the proper management of ESXi services and sets the acceptance level and ssh/ESXi shell banner.

### Purpose

Modify ESXi advanced settings, acceptance level, stopping services and adding esxi shell and ssh banners according to Atos TSS Document.

### Audience

- Devsecops Team.

## Scope

1. Adding banner after logging and using SSH/ESXiShell
2. Modify ESXi advanced settings
3. Change ESXi Security Acceptance Level
4. Stop ESXi security services

## Documentation

[Atos TSS Documents](https://atos365.sharepoint.com/sites/100000120/PublishedStorage/Forms/RACGTSS.aspx)

## Usage

1. Clone the repository containing this playbook.
2. Run the playbook using the below command.
3. If you use -e username=dasID@domain, there will be only the password prompt, if not there will be a prompt for both username and password.
4. Based on the output, there will be a small HTML report called /tmp/reportEsxi.html which will tell you which and if any changes happened during the execution.

```bash
ansible-playbook hardeningEsxi8.yml -e username=dasID@domain
