# Create Cronjob Template

## Changelog

 |    Date    |   TOS   |   Issue   |    Author     |      Description       |
 |------------|---------|-----------|---------------|------------------------|
 | 24.03.2026 | VCS 2.0 | VCS-17886 | Adriana Slabu | Initial draft creation |

## Introduction

This document provides the steps that you need to follow to create cronjob template on `<locationCode>`ans001 server for root user.

### Purpose

The purpose of the cronjob template is to minimize the risk of human error when adding new cronjobs to a customer location.

### Audience

- DevSecOps Team

## Scope

1. Create cronjob template
2. Enforce process for updating cronjobs

### Prerequisites

A new process has been established for **permanently** adding or updating crontab entries. The cronjob template will serve as the single source of truth for all cronjobs and will be updated for each permanent entry. This approach aims to minimize errors, such as typos or misconfigurations.

### Implementation steps in creating the cronjob template

1. Create directory for the template file:

    ```shell
    sudo mkdir -p /opt/cron
    ```

2. Create custom template file (different for each location and it should reflect the correct and already crontab scheduled jobs):

    ```shell
    sudo vi /opt/cron/master.cron
    ```

    > **_NOTE:_** Example of Template for a test environment:

    ```shell
    # Schedule CIS Checks via crontab
    0 01 * * * su - root -c /sysmgt/bin/cis.checks.ubuntu.sh 1>/sysmgt/log/cis.checks.ubuntu.log 2>&1
    # Run AIDE check - uncomment if needed
    #0 5 * * * /usr/sbin/aide --check
    #Ansible:  force creating the directory for the target service account
    5 * * * *  /usr/bin/sudo /bin/su - svc-nes31-ans02
    #Ansible: cronjob to run SRM report
    5 0 * * 5 /usr/local/bin/py3venv/1040-std/bin/ansible-playbook /opt/dhc/manage/configureSrmReport.yml --tags execute &>/dev/null
    #Ansible: Detect no owner files
    @hourly find /* -xdev -path /proc -prune -o -type f \( -nouser -o -nogroup \) -exec chown dhcdummy:dhcdummy {} \; -exec echo Added file {} to dhcdummy,$(date +%H:%M:%S,%d/%m/%Y)>> /var/log/audit/dhcdummy.log \;
    #Ansible: Detect no owner directories
    @hourly find /* -xdev -path /proc -prune -o -type d \( -nouser -o -nogroup \) -exec chown dhcdummy:dhcdummy {} \; -exec echo Added directory {} to dhcdummy,$(date +%H:%M:%S,%d/%m/%Y)>> /var/log/audit/dhcdummy.log \;
    ```

3. Lock it down:

    ```shell
    sudo chown root:root /opt/cron/master.cron
    sudo chmod 600 /opt/cron/master.cron
    ```

4. Create an enforcement script to only update if changes detected:

    ```shell
    sudo vi /opt/cron/enforce_cron.sh
    ```

   This script will contain the following:

    ```shell
    #!/bin/bash
    TEMPLATE="/opt/cron/master.cron"
    CURRENT="/tmp/current.cron"
    crontab -l > "$CURRENT" 2>/dev/null
    if ! diff -q "$TEMPLATE" "$CURRENT" >/dev/null; then
       crontab "$TEMPLATE"
       echo "$(date) - Cron updated (drift detected)" >> /var/log/cron_enforce.log
    else
       echo "$(date) - No changes" >> /var/log/cron_enforce.log
    fi
   ```

5. Make it executable:

    ```shell
    sudo chmod +x /opt/cron/enforce_cron.sh
    ```

6. Add a cronjob that enforces this template. Run:

    ```shell
    crontab -e
    ```

   And add one line (this will become "self-healing"):

    ```shell
    # Weekly (Monday 06:00)
    0 6 * * 1 /opt/cron/enforce_cron.sh
    ```

7. Add it also inside the template:

    ```shell
    # Weekly (Monday 06:00)
    0 6 * * 1 /opt/cron/enforce_cron.sh
    ```

8. Perform backup of current cronjob before enforcing the template, by running:

    ```shell
    crontab -l > /tmp/backup.cron
    ```

9. (Optional) Compare before enforcing the template (Shows what would change without touching anything):

    ```shell
    diff /opt/cron/master.cron <(crontab -l)
    ```

10. Apply template:

    ```shell
    crontab /opt/cron/master.cron
    ```

11. Verify:

    ```shell
    crontab -l
    ```

12. (Optional) Rollback if needed:

    ```shell
    crontab /tmp/backup.cron
    ```
