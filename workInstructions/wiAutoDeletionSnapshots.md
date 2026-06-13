# Auto Deletion of Snapshots using Playbook

## Changelog

|    Date    |   TOS   |   Issue   | Author                | Description |
|------------|---------|-----------|-----------------------|-------------|
| 11-01-2023 | VCS 1.0 | CESDHC-4587  | Krishnasai Dandanayak | Initial document creation |
| 06-03-2024 | VCS 1.7, VCS 1.8 | VCS-12326 | Krystian Bibik | Added configureSnapshotsAutoDeletion.yml playbook usage |

## Introduction

### Purpose

Configure cron job for snapshotsAutoDeletion.yml playbook.

### Audience

- VCS Operations

### Scope

- Configuration of cron job for snapshotsAutoDeletion.yml playbook (manual or using configureSnapshotsAutoDeletion.yml playbook)

## Pre-Requisite

Need to check the policies in the Hashi Vault for the certificate.

## Procedure

The snapshotsAutoDeletion.yml playbook provides solution for automatic deletion of Virtual Machine snapshots that are 7 days old or older (default value can be customized) and have not been assigned the "keepSnapshots: yes" tag to Virtual Machine.

To schedule a cron job use the certificate for logging into Hashi Vault from where it picks the vCenter credentials to login and perform the task.

1. Login to Hashi Vault using our domain credentials.

2. Click on the policies tab and check the account name and account policy which is required.

3. Select the automation5access user account and check the policies whether it has access to pick the vCenter credentials.

4. If the certificate policy doesn't contain access to pick the vCenter credentials then need to update the policy.

5. Raise a change for reference and update the policy as mentioned in the below comment.

      ```txt
         path "secret/data/cis/hrk01/activedirectory/svc-hrk01-vcs01" {
            capabilities = ["read", "list"]
            }
       ```

6. So now the playbook will be running without any manual intervention required to provide the credentials.

7. To configure automatically Cron Job for snapshotsAutoDeletion.yml playbook, please use the configureSnapshotsAutoDeletion.yml playbook. (Steps 8-12 will be skipped)

      ```yaml
      ansible-playbook configureSnapshotsAutoDeletion.yml
      ```

8. To configure manually Cron Job for snapshotsAutoDeletion.yml playbook, please login to the Ansible server (ans001).

9. Cron job needs to scheduled using root user privileges. So login to the ansible server using root credentials or use the "sudo" before the command.

      ```bash
         eg: sudo crontab -e
      ```

10. Provide the name of the cron job as shown below:

      ```txt
         eg: #Ansible: cron job to delete snapshots older than 7 days
      ```

11. Provide the schedule to run the cron job as per the below command.

      ```bash
         MM HH DD Month DOW USERNAME /path
         Where:
         MM: Minutes (0-59 range)
         HH: Hours (0-23 range)
         DD: Days (0-31 range)
         Month: Months (0-12 range)
         DOW: Days of the week (0-7. Starting from Monday, 0 or 7 represents Sunday)
      ```

12. Here by using the below command schedule the job daily at 12:00 AM to remove Virtual Machines snapshots which are 3 days old or more from vCenter `<locationCode>vcs001` (customized playbook values)

      ```bash
         0 0 * * * /opt/dhc/py3venv/ans210-std/bin/ansible-playbook /opt/dhc/manage/snapshotsAutoDeletion.yml -e "vmSnapshotAge=3 vCenterServer=<locationCode>vcs001" -i /opt/dhc/manage/hosts &>/tmp/snapshotslog
      ```

      Example of using the below command schedule the job daily at 12:00 AM to remove Virtual Machines snapshots which are 7 days old or more from vCenter `<locationCode>vcs001` (default playbook values)

      ```bash
         0 0 * * * /opt/dhc/py3venv/ans210-std/bin/ansible-playbook /opt/dhc/manage/snapshotsAutoDeletion.yml -i /opt/dhc/manage/hosts &>/tmp/snapshotslog
      ```

> We can also use a dedicated configureCronJobVmSnapshotsAutoDeletion.yml playbook to automatically configure Cron Job.
>
> ```yaml
> ansible-playbook configureCronJobVmSnapshotsAutoDeletion.yml
> ```
>

### Note : This is valid and updated for BTN environment as of now and if it want to be used in other environments then please check the point number 5 and update in respective environment Hashi Vault
