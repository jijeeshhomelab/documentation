# Reverse Proxy (Nginx) app Upgrade to Latest Version

## Table of Contents

- [Reverse Proxy (Nginx) app Upgrade to Latest Version](#reverse-proxy-nginx-app-upgrade-to-latest-version)
  - [Table of Contents](#table-of-contents)
  - [List of Changes](#list-of-changes)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Manual Upgrade procedure](#manual-upgrade-procedure)
  - [Automation of Upgrade procedure](#automation-of-upgrade-procedure)

## List of Changes

| Version | Date       | Description   | Author                |
| ------- | ---------- | ------------- | --------------------- |
| 0.1     | 19.06.2025 | First version | Krishnasai Dandanayak |
| 0.2     | 30.06.2025 | Added details for prod execution and Dev execution | Krishnasai Dandanayak |

## Introduction

### Purpose

Upgrade the Reverse Proxy app (Nginx) to the latest version using an automation method.

### Audience

- VCS Operations

### Scope

- Upgrade the Reverse Proxy app (Nginx)

## Related Documents

N/A

## Prerequisites

- Reverse Proxy must be configured on the proxy servers.

## Manual Upgrade Procedure

- Take snapshots of both proxy servers before starting the activity.
- Access the Nessus UI via browser and review the scan results to identify any vulnerabilities related to Nginx.
- SSH into one of the proxy servers.
- Verify the current Nginx version installed:

    ```shell
    nginx -v
    ```

- Remove the broken PPA repository

    ```shell
    sudo add-apt-repository --remove ppa:nginx/stable -y
    ```

- Update APT packages

    ```shell
    sudo apt update
    ```

- Add the correct NGINX repository and key  
  - Import the NGINX signing key:

    ```shell
    curl -o /usr/share/keyrings/nginx_signing.key https://nginx.org/keys/nginx_signing.key
    sudo gpg --dearmor /usr/share/keyrings/nginx_signing.key > /usr/share/keyrings/nginx-archive-keyring.gpg
    ```

  - Add the NGINX repository:

    ```shell
    echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] {{ nginxPackagesUrl }} {{ ansible_distribution_release | lower }} nginx"
    ```

- Update APT packages again:

  ```shell
  sudo apt update
  ```

- Install the latest version of Nginx

  ```shell
  sudo apt install nginx -y
  ```

- Check the installed Nginx version:

  ```shell
  nginx -v
  ```

- Restart and enable Nginx

  ```shell
   sudo systemctl restart nginx
   sudo systemctl enable nginx
  ```

- Check the Nginx service status:

  ```shell
  sudo systemctl status nginx
  ```

- Test the Nginx configuration:

  ```shell
  nginx -t
  ```

- After successfully completing the above steps on one server, repeat the same steps on the second proxy server.
- After the upgrade, review the Nessus scan results again to ensure no Nginx-related vulnerabilities remain.

## Automation of Upgrade procedure

- The following playbook automates all the steps mentioned above, **except for reviewing the Nessus scan reports** before and after the upgrade:

- The above playbook can be executed in the dev environments which can be useful for scheduling cronjob to perform the activity.

  ```shell
  ansible-playbook reverseProxyPatching.yml -e "run_env=dev"  # This is for Dev Environment purpose
  ```

- After successful testing in the development environment, the same playbook can be executed in the production environment:

  ```shell
  ansible-playbook reverseProxyPatching.yml -e "run_env=prod"  # This is for Prod Environment purpose
  ```

- Running the playbook in production should be done only after validating the execution results from the Dev environment to ensure stability and reliability.
