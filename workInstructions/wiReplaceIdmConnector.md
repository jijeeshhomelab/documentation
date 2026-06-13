# Replacement of vIDM Connector to AD with LDAP method instead of IWA

## Table of contents

## Changelog

|    Date    |   TOS   |   Issue   | Author | Description |
|------------|---------|-----------|--------|-------------|
| 13.03.2024 | VCS 1.8.2 | VCS-12207 | Jakub Zielinski | Initial Commit |

## Introduction

### Purpose

Replacement of vIDM Connector to AD with LDAP method instead of IWA

### Audience

- VCS Engineers
- VCS Operations

### Scope

This instruction covers configuring the following items:

- Leaving the domain on IDM001
- Deleting the directory
- Re-adding directory with LDAP authentication
- Syncing users
- Testing

## Connector Replacement

### Prerequisites

- Log in to Hashi Vault and test IDM001 credentials to make sure that they work correctly before performing the operation
- Take a snapshot of IDM001
- Test access with your AD account to VMware Aria Operations Network Insight, Aria Operations for Logs, Aria Operations, NSX-T, etc.

### Replacement

Log in to IDM and navigate to `Identity & Access Management`

Navigate to the `Identity & Access Management` -> `Setup` -> click `Leave Domain`

![leave_domain](images/replaceIdmConnector/VNI-000009.png)

Supply your credentials and click `Leave Domain`

![leave_domain_2](images/replaceIdmConnector/VNI-000010.png)

Navigate to the `Identity & Access Management` -> `Directories` -> Select the directory you wish to remove and click  `Delete`

![delete_directory](images/replaceIdmConnector/VNI-000011.png)

Navigate to the `Identity & Access Management` -> `Directories` ->  `Add Directory` -> Select `Add Active Directory over LDAP/IWA`

![add_directory](images/replaceIdmConnector/VNI-000012.png)

Select `Active Directory over LDAP` and settings as on the screenshot below. STARTTLS is not required.

![select_ad](images/replaceIdmConnector/VNI-000013.png)

Configure the Bind User Details similarly to the screenshot below. Fetch credentials for svc-.....-idm001 from HashiVault. Click `Test Connection`. If the connection is successful click `Save & Next`.

![test_connection](images/replaceIdmConnector/VNI-000038.png)

Fill in the 'Specify the group DNs' as shown on the screen below and click the `+` button.

![add_dn](images/replaceIdmConnector/VNI-000039.png)

Check the `Select All` checkbox and click `Next`

![select_all](images/replaceIdmConnector/VNI-000040.png)

On the `Select the Users you would like to sync` click `Next`

![select_users](images/replaceIdmConnector/VNI-000042.png)

On the following page click `Sync Directory`

![sync_directory](images/replaceIdmConnector/VNI-000043.png)

You will be prompted with a message that the sync has started.

![sync_started](images/replaceIdmConnector/VNI-000044.png)

Click `Refresh Page` and notice that groups and users have been synced.

Go to `Identity & Access Management` -> `Setup` -> click `Join Domain`

![join_domain](images/replaceIdmConnector/VNI-000052.png)

### Testing and clean-up

- Test access with your AD account to VMware Aria Operations Network Insight, Aria Operations for Logs, Aria Operations, NSX-T, etc.
- Remove Snapshots
