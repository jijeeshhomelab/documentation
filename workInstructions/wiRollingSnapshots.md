# Rolling Snapshots

## Changelog

|    Date    |   TOS   |   Issue   |    Author     | Description     |
|------------|---------|-----------|---------------|-----------------|
| 29.01.2026 | VCS 2.0 | VCS-17907 | Adam Szymczak | Initial version |

## Introduction

### Purpose

Describe how to configure and use manageRollingSnapshots.yml playbook for daily creation of VM snapshots.

### Audience

- VCS Operations

### Scope

This document covers the following items:

- manageRollingSnapshots.yml usage prerequisites
- manageRollingSnapshots.yml operational usage

## Configuration Steps

1) SSH to Ansible Core VM (`ans001`)
2) Open `mailtoRecipients.yml` file found in `/opt/dhcConfig/group_vars/all` directory
3) Put email addresses of recipients for Rolling Snapshots report in the file following below format and save the file

   ```yaml
   rollingSnapshots:
     mailTo:
     - example1@domain.com
     - example2@domain.com
   ```

4) Run `manageRollingSnapshots.yml` playbook from manage phase with tag for cron configuration:

   ```bash
   ansible-playbook manageRollingSnapshots.yml -t configureCron
   ```

   This will configure daily cronjobs to run at 3 AM for snapshot creation and at 12 PM for validation.
   The execution time can be changed by providing `cronSchedule` and `cronScheduleValidation` extra variables.
   In example below, execution times are changed to 4 AM and 10 AM respectively.

   ```bash
   ansible-playbook manageRollingSnapshots.yml -t configureCron -e "cronSchedule=0 4 * * *" -e "cronScheduleValidation=0 10 * * *"
   ```

5) In vCenter create tag category `automatedRollingSnapshot`
6) Assign the tag with value `enabled` to any VMs that should have snapshot taken daily

## Daily Operations usage

Playbook when set up as cron runs daily at 3 AM for snapshot management and at 12 PM for validation.

Snapshot management tasks can be ran separately using below command:

```bash
ansible-playbook manageRollingSnapshots.yml -t manageSnapshots
```

Validation and reporting tasks can be ran using following command:

```bash
ansible-playbook manageRollingSnapshots.yml -t validate
```

Playbook works as follows:

- Common initial tasks:
  - vcs01 service account is picked up from Vault
  - VM information is fetched from workload domain vCenter
  - VMs with `automatedRollingSnapshot:enabled` tag are filtered out and saved to variable
- Snapshot management tasks:
  - Snapshots with naming template `automatedRollingSnapshot-YYYY-MM-DD` are created on VMs
  - Automation snapshots from previous days are removed if new one was created successfully
- Validation and reporting tasks:
  - Snapshot information for tagged VMs is gathered
  - Report is built using the information (VM name, Snapshot name, Timestamp, Status)
  - Report is sent to any recipients defined in group_vars variable

The cron job at 3 AM executes the whole workflow, while the one at 12 PM only runs the reporting task to ensure the previous run was successful.
Report delivered by email is named `<locationCode> Rolling snapshots report YYYY-MM-DD`.
