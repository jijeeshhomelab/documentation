# RBAC Manual Implementation

# List of Changes
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 16/12/2019 | First version | Maciej Losek |
| 0.2     | 20/01/2020 | vIDM added    | Maciej Losek|
| 0.3     | 01/04/2020 | Group synchronization    | Przemyslaw Bojczuk |
| 0.4     | 30/10/2020 | Removing group synchronization and sync schedule time (automated)| Robert Kaminski |

## Introduction

### Purpose

Implementation RBAC for different VCF components manually.

### Audience

- VCS Operations

### Scope

The scope of this document covers the following:

- manual implementation of RBAC for SDDC Manager
- manual implementation of RBAC for VMware Identity Manager

## Related Documents

- [ROLE BASED ACCESS CONTROL](../design/lldDhcRoleBasedAccessControl.md)

# RBAC implementation

## SDDC Manager

SDDC Manager installation is already automated - deployed during VCF bring-up process.

### Prerequisites

Authentication to the SDDC Manager Dashboard uses the VMware vCenter Single Sign-On authentication service that is installed with the Platform Services Controller feature during the bring-up process for Cloud Foundation system.
So first PSC has to be joined to Active Directory and then authentication source has to be configured.

### Deployment steps

Cloud Admin role can be assign to AD users or groups so that they can log in to SDDC Manager with their AD credentials. The Cloud Admin role has read, write, and delete privileges (including password management).
In this case rsce-{locationCode}-sdm-l-cloudadmins group  should have administrative access to VMware SDDC Manager

To assign Cloud Admin role to AD users or groups so that they could log in to SDDC Manager with their AD credentials below steps have to be followed:

1. Log in to the SDDC Manager Dashboard with superuser credentials ( i .e <administrator@vsphere.local>).
2. Click Administration > Users.
3. Click '+ User or Group'.
4. Filter by domain first and find proper group: "rsce-{locationCode}-sdm-l-cloudadmins"
5. Click the check box next to the group
    Scroll down to the bottom of the page and click Add.
The Cloud Admin role is assigned to the selected group.

## VMware Identity Manager

VMware identity Manager has to be already joined to Active Directory.

### Deployment steps

1. In the VMware Identity Manager console Roles tab, select the role and click Assign.
2. Enter a name in the search box and select group: rsce-dhc-idm-l-superadmin for Super Admin role. Click 'Save'
3. Enter a name in the search box and select group: rsce-dhc-idm-l-readonly for ReadOnly Admin role.  Click 'Save'

NOTE!!!Only groups with fewer than 500 users in the group can be promoted to an administrator role.

The user profile page is updated to show the role.
