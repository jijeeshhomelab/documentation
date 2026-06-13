# Title: Enable new service accounts in VCS 2.1

## List of Changes

| Date       | Issue     | Author          | TOS  | Description            |
| ---------- | --------- | --------------- | ---- | ---------------------- |
| 17/06/2025 | VCS-16100 | Adam Szymczak   |      | Initial version        |
| 22/09/2025 | VCS-15947 | Adam Szymczak   |      | Adjust WI for svc playbook changes |
| 20/10/2025 | VCS-16100 | Adam Szymczak   |      | Refactor to be separate WI |

## Introduction

This instruction describes steps required to enable new service accounts.
One account is going to be used to access Aria Operations.
Second account is going to be enabled to be used with NSX.

## Scope

The work instruction is intended to cover below tasks:

- Create vROPS service account
- Enable NSX service account
- Add automated service account password rotation to cron

## Implement new service accounts

### Create vROPS service account

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook createServiceAccount.yml
```

When prompted provide following details:

- Service name - "vop"
- Service account number - "01"
- Group name - "rsce-dhc-vop-l-admins"

### Configure vROPS service account

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook configureVropsServiceAccount.yml
```

This playbook will configure resource group permissions in vROPS.

### Enable NSX service account

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/update* folder.

```bash
ansible-playbook enableNsxServiceAccount.yml
```

This playbook will:

- Enable nsx01 service account if it's disabled
- Add nsx01 service account to rsce-< locationCode >-nsx-l-enterpriseadmins AD group

### Add automated service account password rotation to cron

Execute the following playbook on *ans001* server from */home/axxxxx/dhc/manage* folder.

```bash
ansible-playbook configurePasswordRotationCron.yml -t serviceAccounts
```

This playbook will add crontab entry on ans001 for execution of resetAdServiceAccount.yml playbook.
