# Manage user accounts in Active directory and vRA Cloud

- [Manage user accounts in Active directory and vRA Cloud](#manage-user-accounts-in-active-directory-and-vra-cloud)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
- [Active Directory account actions](#active-directory-account-actions)
  - [Manage accounts using automation](#manage-accounts-using-automation)
  - [Authorization matrix report](#authorization-matrix-report)
  - [Create account](#create-account)
  - [Assign Role Group](#assign-role-group)
  - [Disable/remove account](#disableremove-account)
  - [Password reset](#password-reset)
- [vRA account actions](#vra-account-actions)
  - [Manage accounts using automation](#manage-accounts-using-automation-1)
  - [Add account to tenant/organization](#add-account-to-tenantorganization)
  - [Edit assigned roles](#edit-assigned-roles)
  - [Remove account from tenant/organization](#remove-account-from-tenantorganization)

## Changelog

| Date | Issue | User | Changes |
|---------|------|------|---------|
| 15.03.2022 | DHC-3588 | Adam Szymczak | Initial version |
| 28.09.2023 | VCS-10948 | Adrian Ilea | Adding Description field requirements for AD account creation|
| 21.11.2023 | VCS-11494 | Adam Szymczak | Adding Email field requirements for AD account creation|

## Introduction

### Purpose

Perform actions related to user accounts in Active Directory and vRA Cloud.

### Audience

### Scope

- Create AD account
- Assign roles
- Reset passwords
- Add AD account to vRA
- Remove AD account from vRA

# Active Directory account actions

All following actions are executed in **Active Directory Users and Computers** which can be accessed from **Windows** VMs in environment such as **Terminal Server** or **Active Directory Controller**. All accounts are placed in **{ Domain Name } -> VCS -> Users -> DHCAdmins** folder and before creating new ones engineer should navigate to that directory.

## Manage accounts using automation

Alternatively Active Directory accounts can be managed using **manageAdAccounts** playbook from manage phase.

```markdown
ansible-playbook manageAdAccounts.yml
```

To do so csv input file must be prepared which follows format shown below.

| DAS | name | surname | email | RG | Team | Action |
|---------|------|------|---------|------|------|---------|
| A763504 | Margo | Piliukh | `marharyta.piliukh@atos.net` | PlatformAdministrators | DevSecOps Team | Add |

For more details on how to use the playbook and how to prepare input file go to **README.md** file found in **dhc-manageAdAccounts** role.

## Authorization matrix report

**Authorization Matrix** report must be generated each time changes are made to **Active Directory**. To do so run following command:

```markdown
ansible-playbook createAuthorizationMatrixReport.yml
```

The report should be then uploaded to Sharepoint of **CES Evidence Repository** under the appropriate customer folder.

## Create account

1. In top bar click **Create a new user in current container** button
2. In **New Object - User** window fill out following data
   - **First/Last name** - name of person that will use the account
   - **User logon name** - the ID of person that will use account - Atos HR ID by default
3. Click **Next** and in **Password** fields type in secure password that complies with the set policy
4. Make sure all checkboxes are not selected and click **Next** and then **Finish**
5. Assign the account to appropriate **Role Group**
6. User Description must contain the VCS Team name or the exact  "**Customer User**" two-word phrase if the account is created for the customer (used for the [CustUsersReport](wiCustUsersReport.md)). Add also the RITM number of the VCS access request in Description.
7. Assign E-mail address to account - it will be used to notify user about approaching password expiration
8. For the VCS Engineer account send credentials directly to the user in encrypted email. For the customer account send the login details to TSM via encrypted document to deliver it to the person for whom the account was created.

## Assign Role Group

1. Right click on account that will be modified and click **Properties**
2. Go to **Member Of** tab
3. Click **Add...** and type in role name to be assigned in the window
4. Click **Check Names** to confirm role name and then **OK**
5. If any role is to be removed select it on the list and click **Remove** and confirm with **Yes**
6. Confirm new role assignment with **OK** button

## Disable/remove account

1. Right click on account to be disabled/removed and click either **Disable Account** or **Delete** appropriately
2. If account is to be deleted confirm with **Yes** at the dialog box

## Password reset

1. Right click on user for which password is to be reset and click **Reset Password...**
2. Type in new secure password in both fields
3. Keep **User must change password at next logon** unchecked and check **Unlock the user's account** if it's locked
4. Confirm with **OK** and send new password to account owner through encrypted mail

# vRA account actions

To perform vRA account actions first login to **VMware Cloud Services Console**. Then switch organization to one on which accounts will be managed, it can be either provider organization or tenant. After loading target organization go to **Identity & Access Management** menu.

## Manage accounts using automation

Alternatively vRA accounts can be managed using **manageVraCloudUsers** playbook from manage phase. To do so csv input file must be prepared. For details how to use the playbook and how to prepare input file check [**wiManageVraUsersAndRoles**](./wiManageVraUsersAndRoles.md) document. However the automation only works on tenant level access for now.

## Add account to tenant/organization

1. Click **Add Users**
2. In next window type in email addresses of users that are to be added - it has to be mail used for VMware account
3. Select appropriate roles that new users will have in **Role Assignment** section
4. If role other than **Customer Administrator** is selected specify what access will be granted for each service
5. Click **SAVE**
6. If user does not have VMware account with added mail they will receive mail invitation to create an account, otherwise access will be available to added users

## Edit assigned roles

1. Select user on **Active Users** list, only one can be edited at once
2. Click **Edit Roles**
3. Modify assigned roles accordingly
4. Click **SAVE** to apply changes

## Remove account from tenant/organization

1. Select users from **Active User** list that will be removed
2. Click **Remove Users**
3. Confirm action by clicking **Remove**
