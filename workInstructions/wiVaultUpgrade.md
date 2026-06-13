# Hashi Vault app Upgrade to Latest Version

## Table of Contents

- [Hashi Vault app Upgrade to Latest Version](#hashi-vault-app-upgrade-to-latest-version)
  - [Table of Contents](#table-of-contents)
  - [List of Changes](#list-of-changes)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Manual Upgrade procedure](#manual-upgrade-procedure)
    - [Download Vault archive file](#download-vault-archive-file)
    - [Upgrade Vault binary](#upgrade-vault-binary)
  - [Automation of Upgrade procedure](#automation-of-upgrade-procedure)
  - [Validations](#validations)

## List of Changes

| Version | Date       | Description           | Author                |
| ------- | ---------- | --------------------- | --------------------- |
| 0.1     | 21.02.2023 | First version         | Lukasz Tomaszewski    |
| 0.2     | 19.06.2025 | Added Automation part | Krishnasai Dandanayak |
| 0.3     | 30.06.2025 | Added details for prod execution and Dev execution | Krishnasai Dandanayak |

## Introduction

### Purpose

Manually upgrade HashiCorp Vault application.

### Audience

- VCS Operations

### Scope

1. Upgrade HashiCorp Vault

## Related Documents

N/A

## Prerequisite

N/A

## Manual Upgrade procedure

### Download Vault archive file

1. Before you download Vault archive, you need to check the version in versionMatrix.json (`https://github.com/GLB-CES-PrivateCloud/DHC-Version-Matrix` -> select at least DHC-1.7 branch from dropdown list and search for "component": "hashiVault") which needs to be downloaded.
2. Go to `https://developer.hashicorp.com/vault/downloads`, select required version and download AMD64 binary for Linux.
3. Transfer the file to HashiCorp Vault server (in example via WinSCP) into your HOME directory.

### Upgrade Vault binary

1. Login to Vault server and execute:

    ```shell
    sudo systemctl stop vault
    sudo unzip vault_x.x.x_linux_amd64.zip
    sudo cp vault /usr/local/bin/
    sudo reboot
    ```

2. After the reboot, login to Vault url and check if you're able to access web application.

## Automation of Upgrade procedure

- The following Ansible playbooks automate the upgrade process in both development and production environments, supporting upgrades to either a specific version or the latest version by default.

**All the following playbooks perform similar tasks as described below:**

- Take a snapshot of the Vault server.  
- Perform a Vault backup including sealed keys.  
- Update the repository from the Debian server to install either the latest or a specific version of Vault binaries.  
- Stop the Vault service.  
- Rename the existing Vault binary to `vault-old`. Update the unseal shell script with the path to the new Vault binary, and rename the old script to include `-old` for future reference if needed.  
- Reboot the server.  
- Continuously ping the server until it is fully up and running.  
- Validate that the Vault service is running successfully.

  ```shell
  ansible-playbook patchVaultToLatestVersion.yml -e "run_env=dev"  # This playbook for latest upgrade on Dev environment
  ```

  ```shell
  ansible-playbook patchVaultToLatestVersion.yml -e "vaultVersion=1.xx.xx-1" -e "run_env=dev"  # This playbook for specific upgrade on Dev environment
  ```

  ```shell
  ansible-playbook patchVaultToLatestVersion.yml -e "run_env=prod"  # This playbook for latest upgrade on Prod environment
  ```

  ```shell
  ansible-playbook patchVaultToLatestVersion.yml -e "vaultVersion=1.xx.xx-1" -e "run_env=prod"  # This playbook for specific upgrade on Prod environment
  ```

## Validations

- Login to Hashi Vault from browser.
- Create test user in vault.
- Remove the test user from vault.
