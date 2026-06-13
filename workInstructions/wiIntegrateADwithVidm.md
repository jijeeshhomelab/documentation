# Integration of Active Directory (AD) with VMware Identity Manager (vIDM)

## Table of Contents

- [Integration of Active Directory (AD) with VMware Identity Manager (vIDM)](#integration-of-active-directory-ad-with-vmware-identity-manager-vidm)
  - [Table of Contents](#table-of-contents)
  - [List of Changes](#list-of-changes)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
  - [Prerequisites](#prerequisites)
  - [Integration Steps](#integration-steps)
  - [Important Consideration: Directory Search Attribute](#important-consideration-directory-search-attribute)

## List of Changes

| Version | Date       | Author       | Issue    | Changes           |
|---------|------------|--------------|----------|-------------------|
| 0.1     | 10.10.2024 | Rachel Beulah | VCS-17234 | Document creation |

## Introduction

### Purpose

This guide explains the detailed steps to integrate a customer’s Active Directory (AD) with VMware Identity Manager (vIDM). The goal is to configure vIDM to synchronize users and groups from AD, enabling authentication and access management.

## Prerequisites

- **Service Account:** A dedicated service account in the customer Active Directory (AD) is required for integration with vIDM. This account should have sufficient permissions to read user and group information within AD. The same account will be used by vIDM to query and synchronize user data from AD.
- **Connectivity:** To establish connectivity between the VMware Identity Manager (vIDM) server and the customer’s Active Directory (AD), ensure that firewall rules permit traffic for essential directory services such as LDAP (389), LDAPS (636), Kerberos (TCP/UDP 464), Global Catalog, and MS-DS. These protocols enable secure authentication, directory lookups, and synchronization between vIDM and AD.
- **Test Connection:** Before proceeding, test and verify the connection between vIDM and AD to confirm credentials and network paths are correct.

## Integration Steps

### Step 1: Log in to vIDM

- Log in to the vIDM console using an admin account.
- Navigate to:
Identity & Access Management → Directories → Add Directory → Active Directory over LDAP

> Figure: Option to select - Active Directory over LDAP

![image](/workInstructions/images/wiIntegrateADwithVidm/addDirectory.png)

### Step 2: Directory Configuration

#### Provide Directory Details

- **Directory Name:** Assign a clear name that identifies the customer domain, e.g. "CustomerDomain".
- **Sync Connector:** Select the synchronization connector associated with the environment. This is the component that handles data sync operations.
- **Directory Search Attribute:** Select ```UserPrincipalName``` instead of the default sAMAccountName.

> This ensures that users are uniquely identified across multiple domains.
Example: ```user@dhc.local```

#### Specify Server Location

There are two options:

- **Use DNS Service Location:** If the customer’s AD supports DNS Service Location records, select this option to let vIDM discover domain controllers automatically.
- **Manual Host and Port:** If not, deselect the box and enter the specific AD server IP and port.
  - **Server Host:** Enter the IP or hostname of the customer AD server.
  - **Server Port:** Default: 389 for LDAP or 636 for LDAPS.

#### Bind User Details

This is the account vIDM uses to read user and group data from AD.

- **Base DN:** Defines where vIDM starts searching for accounts.
  Example: ```dc=example, dc=com``` limits searches under this organizational unit.
- **Bind DN:** The distinguished name of the service account that vIDM uses to connect to AD.
  Example: ```CN=svc-idm,OU=ServiceAccounts,DC=example,DC=com```
- **Bind Password:** Password for the service account.

#### Test Connection

Provide the service account password and run a connection test. This verifies that vIDM can communicate with AD with the provided credentials and network details.

- If successful, proceed to the next step.
- If unsuccessful, verify network connectivity, firewall rules, and service account permissions.

> Figure: Example integration of dhc.local domain in nx4dhc01 vIDM for reference

![image](/workInstructions/images/wiIntegrateADwithVidm/sample1.png)
![image](/workInstructions/images/wiIntegrateADwithVidm/sample2.png)

### Step 3: Domain Selection

Once the connection is successful, vIDM will detect the domain name automatically (e.g., dhc.local). Select the domain and continue.

### Step 4: Mapped Attributes

This step defines which LDAP attributes map to vIDM attributes.
No changes are typically required here — use the defaults provided by vIDM.
Click Next to continue.

### Step 5: Select Groups and Users

**Groups:** Specify the AD groups that should be synced to vIDM.

**Users:** We can filter or specify particular users to be synced. It’s also possible to exclude specific users if needed.

>Figure: Syncing Groups and Users from AD to vIDM

![image](/workInstructions/images/wiIntegrateADwithVidm/groups.png)
![image](/workInstructions/images/wiIntegrateADwithVidm/Users.png)

### Step 6: Review and Sync

- The next screen shows the number of users and groups that will be synchronized.
- Click Save & Sync to begin synchronization.
- Once completed, users and groups from the external domain will appear under Users & Groups in vIDM.

Verify synchronization by checking the user list or by testing login with a user from the integrated domain.

## Important Consideration: Directory Search Attribute

### Why Select “UserPrincipalName” as Directory Search Attribute

When multiple domains are integrated into vIDM, using the same sAMAccountName can cause login conflicts if users in different domains share the same username.

**Example Scenario:**

> User john exists in both nx4dhc01.next and cly1.local
> If both directories use sAMAccountName, vIDM cannot distinguish between [john@nx4dhc01.next](john@nx4dhc01.next) and [john@cly1.local](john@cly1.local)

**Error in Aria Automation (vRA):**

> "You have not been assigned access to any service. Please, contact your administrator."

**Reason:**
Aria Automation identifies users based on the vIDM Search Attribute.
If the same attribute (like sAMAccountName) exists in both domains, it causes conflicts.

**Solution:**
Update the Directory Search Attribute to ```UserPrincipalName``` (e.g., [john@cly1.local](john@cly1.local).
This ensures unique user identification across all domains.

**Reference:**
VMware KB Article [315174](https://eur01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fknowledge.broadcom.com%2Fexternal%2Farticle%2F315174%2Flogin-with-domain-users-into-aria-automa.html&data=05%7C02%7Crachel.packiaraj%40atos.net%7C8e3e5e7e27054d55c7b608ddf7347b58%7C33440fc6b7c7412cbb730e70b0198d5a%7C0%7C0%7C638938526991460102%7CUnknown%7CTWFpbGZsb3d8eyJFbXB0eU1hcGkiOnRydWUsIlYiOiIwLjAuMDAwMCIsIlAiOiJXaW4zMiIsIkFOIjoiTWFpbCIsIldUIjoyfQ%3D%3D%7C0%7C%7C%7C&sdata=LAn2oAsuJFJEiALHOXld7TXTj%2F%2F5BRqNsMWXv42PONw%3D&reserved=0) — Conflicts between users in multiple domains when using sAMAccountName.
