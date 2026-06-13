# Set Aria Operations Dashboard Permission

## Table of Contents

- [Set Aria Operations Dashboard Permission](#set-aria-operations-dashboard-permission)
  - [Table of Contents](#table-of-contents)
  - [List of Changes](#list-of-changes)
  - [Introduction](#introduction)
    - [Scope](#scope)
  - [Manual Steps](#manual-steps)
  - [Automation](#automation)
    - [Requirements](#requirements)

## List of Changes

| Version | Date       | Author       | Issue    | Changes           |
|---------|------------|--------------|----------|-------------------|
| 0.1     | 06.11.2024 | Rachel Beulah | VCS-14257| Document creation |

## Introduction

### Scope

Remove dashboards from the Everyone usergroup.

## Manual Steps

1. Login to vROps.

2. Now go to Dashboards-> Manage. Select the options menu (represented by three horizontal dots) and select 'Manage Dashboard Sharing'.

    ![image](/workInstructions/images/wiSetvRopsDashboardPermission/dashboard_sharing.png)

3. Select 'Everyone' usergroup.

    ![image](/workInstructions/images/wiSetvRopsDashboardPermission/Everyone_usergroup.png)

4. Select all dashboards in Everyone usergroup and click 'STOP SHARING'.

    ![image](/workInstructions/images/wiSetvRopsDashboardPermission/Stop_sharing.png)

5. Dashboards will be removed from Everyone usergroup.

    ![image](/workInstructions/images/wiSetvRopsDashboardPermission/Removed_dashboard.png)

6. Save the configuration.

## Automation

### Requirements

```markdown
apt install -y chromium-chromedriver python3-selenium
```

Execute the following playbook, to remove Dashboards from Everyone Usergroup Permission using Selenium(Python) script.

```markdown
ansible-playbook setvRopsDashboardPermission.yml 
```

- This playbook will check for Everyone Usergroup in 'Manage Dashboard Sharing'.
- Select all dashboards which are under Everyone Usergroup permission.
- Click 'STOP SHARING' and save the configuration.
