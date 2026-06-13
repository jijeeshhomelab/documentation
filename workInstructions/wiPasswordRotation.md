# Password Rotation

## Changelog

| Date       | Issue     | Author                | Description                                                              |
|------------|-----------|-----------------------|--------------------------------------------------------------------------|
| 04.03.2024 | VCS-12331 | Adam Szymczak         | Initial version                                                          |
| 26.04.2024 | VCS-11207 | Stanislaw Kilanowski  | Adjustments after the rework of resetAdServiceAccount.yml                |
| 14.05.2024 | VCS-5856  | Krystian Bibik        | Added information about vRO SRM plugin credentials                       |
| 17.05.2024 | VCS-12630 | Stanislaw Kilanowski  | Described process for vCenter administrator service account              |
| 20.05.2024 | VCS-12486 | Adam Szymczak         | Updated to contain information on service account rotation report        |
| 17.06.2024 | VCS-12626 | Adam Szymczak         | Updated srm01 rotation, added vcs03 and vNI/vNC rotation                 |
| 24.06.2024 | VCS-13152 | Krystian Bibik        | Added information about password rotation for local SRM accounts         |
| 26.07.2024 | VCS-12487 | Adam Szymczak         | Added information about SDDC password rotation report                    |
| 09.08.2024 | VCS-12632 | Piotr Gesikowski      | Added information about CPX admin password rotation                      |
| 21.08.2024 | VCS-13643 | Adam Szymczak         | Added information about password export to CyberArk                      |
| 05.09.2024 | VCS-13835 | Michał Sobieraj       | Amendment to the documentation about rotatePassword.yml playbook changes |
| 07.11.2024 | VCS-12631 | Piotr Gesikowski      | Amendment about automated vRA Secret update for ans03                    |
| 05.12.2024 | VCS-14549 | Stanislaw Kilanowski  | Clarification of vCenter administrator service account process           |
| 16.01.2025 | VCS-14414 | Ciprian Sferle        | Added ORADAD scan and email scheduled task names                         |
| 19.02.2025 | VCS-15219 | Adam Szymczak         | Added vro01 and vra01 service accounts                                   |
| 05.09.2025 | VCS-14306 | Tomasz Korniluk       | Added information about SoxDB credentials rotation                       |
| 04.03.2025 | VCS-15301 | Adam Szymczak         | Added idm01 service account                                              |
| 11.04.2025 | VCS-14552 | Stanislaw Kilanowski  | Corrected the scope of the rotatePassword.yml playbook                   |
| 15.04.2025 | VCS-15510 | Mirela Bogdan         | Modified for vrops 8.18 version steps                                    |
| 05.06.2025 | VCS-15570 | Stanislaw Kilanowski  | Added vop01 service account                                              |
| 06.06.2025 | VCS-15727 | Adam Szymczak         | Move NSX integrations to nsx01 service account                           |
| 03.07.2025 | VCS-15212 | Adam Szymczak         | Adjust for automated service account rotation                            |
| 23.07.2025 | VCS-16726 | Adam Szymczak         | Added vli01 service account rotation                                     |
| 31.07.2025 | VCS-16666 | Stanislaw Kilanowski  | Moved vROPS vCenter plugins update to service account                    |
| 05.08.2025 | VCS-14614 | Michał Sobieraj       | Expand cmp edges password rotation functionality                         |
| 22.09.2025 | VCS-15947 | Adam Szymczak         | Change account for cloud account vcs, add notes on multitenancy          |
| 20.10.2025 | VCS-15150 | Adam Szymczak         | Add information on Operations for Logs adapter                           |
| 08.01.2026 | VCS-15946 | Adam Szymczak         | Extend idm01 update for all tenants                                      |
| 16.02.2026 | VCS-17892 | Stanislaw Kilanowski  | Added vcf01 service account rotation                                     |
| 27.03.2026 | VCS-18245 | Adam Szymczak         | Added instructions on Vault backup                                       |
| 23.04.2026 | VCS-15210 | Michał Braun-Sobieraj | Added hcx01 service account and HCX local accounts rotation              |

## Introduction

### Purpose

Provide steps to be taken during Password Rotation process after partial automation using crontab is implemented.

### Audience

- VCS Operations
- VCS Engineers

### Scope

- Verifying automatically rotated passwords
- Rotation of remaining passwords
- Manual update of integrations where needed

## Verify automatically rotated accounts

For VCS environments, with implemented automated password rotation of selected components, following passwords are rotated automatically:

| Account | Playbook entry in crontab |
|---------|---------------------------|
| SDDC Managed accounts | resetSddcManagerManagedPasswords.yml |
| SDDC Manager local accounts (admin, root, ansible, vcf)<br /> Linux local accounts (next, root, billing-user)<br /> Windows local accounts (c-kathos)<br /> Domain admin (`administrator@domain.next`)<br /> Schedule service accounts (aut01, aut02, aut03)<br /> Compute Edge Node accounts (admin, root, audit)<br /> Witness hosts root accounts (root)<br /> Nessus application user (nessus)<br /> SRM local accounts (root, admin)<br /> LCM local accounts (root, sshuser)<br /> VNI/VNC local accounts (admin@local, consoleuser, support)<br />| rotatePassword.yml |
| Service accounts | resetAdServiceAccount.yml |

>Note:
>Environments don't have to contain all listed accounts depending on used components.

When starting password rotation verify if above passwords were rotated using Password Expiration report. It is scheduled to be created after automated password rotation and is located in `/home/next` directory on Ansible Core VM. If report is not present or is incomplete please verify and fix the issue by checking logs at `/var/log/getPasswordExpiration.log`. After fixing the automated report generate one manually by running `getPasswordExpiration.yml` which will create it at home directory of executing user.

Once report file is opened verify if dates in `Last password rotation` column for listed accounts are first Tuesday of the month. This is time at which automated playbooks are executed by default - if date is correct no further action for those accounts is needed.

In case date doesn't match verify logs for playbook run at `/var/log/` and fix any issues found.
When it's fixed rotate the passwords that weren't rotated automatically as expected by running the playbook.

## Verify automatically refreshed accounts (Hachette only)

For specific VCS environments where there is a need to **refresh**, rather than rotate, the playbook **refreshAdServiceAccount.yml** is to be used.

For already added specific customer service accounts, the following passwords are refreshed automatically:

- svc-{{ locationCode }}-bck02
- svc-{{ locationCode }}-bck03
- svc-{{ locationCode }}-vcs02-readonly

However, other service accounts can also be refreshed by using extra vars, when running playbook:

```shell
ansible-playbook refreshAdServiceAccount.yml -e '{"refreshOnlyAccounts":["account1","account2"]}' -vvv
```

To verify the refresh succeeded, Password Expiration report can be used.

Playbook refreshAdServiceAccount.yml will also create a log file, refresh.log , which will reside in: `/var/log/dhcLog` folder.

Once refreshed, the service account can also be checked manually by using command:

```shell
net user svc-{{ locationCode }}-bck02 /domain
```

Check 'Password last set'. If refresh worked, the date should be updated.

## Verify SDDC Managed accounts automatic password rotation

SDDC Manager managed accounts are rotated automatically.
Additionally, PSC and NSX Manager entities are rotated automatically only on VCS using vRA on-prem.

To verify if accounts were rotated successfully open latest `sddcAccountRotation` report generated in `/home/next` location on Ansible Core VM.
Exact report file name is provided in playbook execution log at `/var/log/resetSddcManagerManagedPasswords.log`.

Analyze the report - confirm `Account Rotation` and `Vault Update` columns have `SUCCESSFUL` status for all accounts (for VCF service accounts `Vault Update` will be `SKIPPED`).
After that make sure all integrations were updated successfully by checking `Integrations Update Status`.

In case password rotation for given entity type fails, rerun it by executing below command:

```shell
ansible-playbook resetSddcManagerManagedPasswords.yml -e entityTypes='< entityName >'
```

For multiple entities run:

```shell
ansible-playbook resetSddcManagerManagedPasswords.yml -e '{"entityTypes":["< entityName1 >","< entityName2 >"]}'
```

In case only one account needs to be rotated run:

```shell
resetSddcManagerManagedPasswords.yml -e accountList='< accountName >@< serverFqdn >'
```

For multiple accounts:

```shell
resetSddcManagerManagedPasswords.yml -e '{"accountList":["< accountName1 >@< serverFqdn1 >","< accountName2 >@< serverFqdn2 >"]}'
```

The playbook can be executed with a tag `integrationsOnly` to skip password reset.
When ran with the tag, password will be moved from SDDC Manager to Vault. After that integrations update will be performed for accounts.
This option can be used in case account is remediated manually on SDDC Manager.

```shell
ansible-playbook resetSddcManagerManagedPasswords.yml -t integrationsOnly
```

Integrations of a specific account can be updated by running:

```shell
ansible-playbook resetSddcManagerManagedPasswords.yml -e accountList='< accountName >@< serverFqdn >' -t integrationsOnly
```

After verifying automated password rotation results, rotate passwords of remaining components by following instructions below.

### SDDC Manager Managed Passwords - VRLI entity

>Note: This entity is rotated automatically along with integration update.

Rotate Aria Operations for Logs accounts by running:

```shell
ansible-playbook resetSddcManagerManagedPasswords.yml -e entityTypes='VRLI'
```

All integrations are updated automatically. However if playbook fails verify and update them using below steps.

#### vROps Adapter

To verify if adapter was updated successfully after Operations for Logs admin account rotation:

- Log in to vROPS instance.
- Go to `Administration -> Integrations` menu on left side.
- Check if VMware Aria Operations for Logs Adapter is showing `OK` Status.
- If any adapter is not working, follow below steps to update manually:
  - Click on adapter menu and then `Edit`.
  - Click crayon button next to `Credential` field.
  - Verify if `Operations for Logs Username` field has `admin`.
  - Put new password in `Operations for Logs Password` field and click `OK`.
  - Click `VALIDATE CONNECTION` and `ACCEPT` certificate if prompted.
  - After successful validation click `SAVE` button.

## Rotate passwords for remaining SDDC managed components

>Note:
>Before resetting the password for entity types `"PSC"` and `"NSXT_MANAGER"`, create vRA Cloud token with createVraCloudToken.yml.
>If there are multiple tenants on the VCS site, you must generate token for each tenant.
>This applies to VCS environments using vRA Cloud.

### SDDC Manager Managed Passwords - PSC entity

>Note: For VCS using vRA on-prem this step is part of automated password rotation.

Rotate vCenter PSC account (`administrator@vsphere.local`) by running:

```shell
ansible-playbook resetSddcManagerManagedPasswords.yml -e entityTypes='PSC'
```

All integrations are updated automatically. However if playbook fails verify and update them using below steps.

#### vRO plugins on Orchestrator

For VCS environments using vRA Cloud:

- Login to `https://< abx001ProxyFqdn >/` VM using VMware Cloud account.
- Go to `Administration -> Inventory`.
- Check if `vSphere vCenter Server` plugins are populated, if not proceed with next steps.
- Go to `Library -> Workflows`, then find `Update a vCenter Server instance` workflow.
- Run the workflow with following details:
  - Will you orchestrate this host? - checked
  - Ignore certificate warnings? - checked
  - Do you want to create a session per user to the vCenter Server? - unchecked
  - Username - `administrator@vsphere.local`
  - Password - password for account from Vault
- Repeat the step for second vCenter in environment.
- Repeat all above steps for all `abx` servers in environment.

#### vROps Adapters

To verify if adapters were updated successfully after PSC rotation:

- Log in to vROPS instance.
- Go to `Administration -> Integrations` menu on left side.
- Check if SRM and vSR adapters are showing `OK` Status.
- If any adapter is not working, follow below steps to update manually:
  - Click on adapter menu and then `Edit`.
  - Click crayon button next to `Credential` field.
  - Verify if `User name` field has `administrator@vshpere.local` - starting with capital `A` for vSR adapter.
  - Put new password in `Password` field and click `OK`.
  - Click `VALIDATE CONNECTION` and `ACCEPT` certificate if prompted.
  - After successful validation click `SAVE` button

#### vRA SaaS vCenter Cloud Account

>Note: This applies only to VCS environments using vRA Cloud.

- Login to vRA Cloud Console.
- Go to tenant connected to VCS environment.
- Go to `Assembler -> Infrastructure -> Cloud Accounts`.
- Check if `<locationCode>vcs001` and `<locationCode>vcs002` accounts have `OK` status.
- Update any accounts with `Warning` status by following below steps:
  - Click on cloud account name.
  - In `Credentials` section update `Password` field.
  - Click `Validate`.
  - When validation is successful click `SAVE`.
- Repeat the steps for all tenants for multi-tenant environments.

### SDDC Manager Managed Passwords - NSXT_MANAGER entity

>Note: For VCS using vRA on-prem this step is part of automated password rotation.

Rotate NSX-T local accounts (admin and root) by running:

```shell
ansible-playbook resetSddcManagerManagedPasswords.yml -e entityTypes='NSXT_MANAGER'
```

All integrations are updated automatically.
However if playbook fails verify and update them using below steps.

#### vRA NSX-T Cloud Account

>Note: These steps apply only to VCS environments using vRA Cloud.

- Login to vRA Cloud Console or `<locationCode>vra001` depending on vRA type used in environment.
- Go to tenant connected to VCS environment if using vRA Cloud.
- Go to `Assembler -> Infrastructure -> Cloud Accounts`.
- Check if `<locationCode>nsx001` and `<locationCode>nsx002` accounts have `OK` status.
- Update any accounts with `Warning` status by following below steps:
  - Click on cloud account name.
  - In `Credentials` section update `Password` field.
  - Click `Validate`.
  - When validation is successful click `SAVE`.
- Repeat the steps for all tenants for multi-tenant environments.

#### vRA on-prem vCenter Cloud Account

>Note: These steps apply only to VCS environments using vRA on prem.

- Login to `<locationCode>vra001`.
- Go to `Assembler -> Infrastructure -> Cloud Accounts`.
- Check if `<locationCode>nsx002` cloud account has `OK` status.
- Update the account with `Warning` status by following below steps:
  - Click on cloud account name.
  - In `Credentials` section update `Password` field.
  - Click `Validate`.
  - When validation is successful click `SAVE`.
- Repeat above steps for all tenants present (`<tenant>.<locationCode>vra001`).

#### vROPS Adapters

To verify if adapters were updated successfully after NSX-T rotation:

- Log in to vROPS instance.
- Go to `Data Sources -> Integrations` menu on left side.
- Check if all NSX-T adapters are showing `OK` Status.
- If any adapter is not working, follow below steps to update manually:
  - Click on adapter menu and then `Edit`.
  - Click pencil button next to `Credential` field.
  - Put new password in `Password` field and click `OK`.
  - Click `VALIDATE CONNECTION` and `ACCEPT` certificate if prompted.
  - After successful validation click `SAVE` button

#### vRNI Data Source

Check vRealize Network Insight to see if the password for the `admin` user has been updated for vCenter Data Sources.
To check, login as AD user to vRNI and go to `Settings/Accounts and Data Sources`.
If there is an issue with data collection, update NSX-T Data Sources with the new password by following the steps below:

- Select the NSX-T Data Sources for which the password will be updated. Click the pencil icon to edit
- Provide the new password and click `VALIDATE` then `SUBMIT`

## Rotate passwords for local accounts

Local accounts are rotated automatically via cronjob. Same action can be performed by the user running the playbook as below:

```shell
ansible-playbook rotatePassword.yml -e executionOption=automation
```

Command will run password rotation for every option covered by playbook.

Playbook also has functionality to rotate accounts only on specified servers. This can be performed using command like in example below:

```shell
ansible-playbook rotatePassword.yml -e executionOption=c-kathos --limit "localhost,tss001" 
```

> [!IMPORTANT]
> Running above command requires localhost entry in the limit section.

To verify if accounts were rotated successfully open latest `rotatePassword_{{ locationCode }}_{{ Date }}T{{ Time }}` report generated in `/home/next` location on Ansible Core VM.
Exact report file name is provided in playbook execution log at `/var/log/rotatePassword.log`.

Analyze the report - confirm `Rotation Result` columns have `SUCCESS` status for all accounts. If report shows `FAILURE` then follow section of manual rotation steps for given component.

### VMware SRM Appliance Management local accounts rotation

To perform SRM local accounts password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=srm
```

Playbook will rotate `root` and `admin` accounts on SRM.

In case of playbook failure, log in to the `{{ locationCode }}srm001` server via SSH and change the above users passwords manually to the one following the ATOS password policy. As the next step update the Hashi Vault entry for the given user.

> [!IMPORTANT]
> The SRM Rotation is applicable only in the active-passive DR sites.

### VMware Lifecycle Manager Appliance sshuser account rotation

To perform LCM sshuser password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=lcm
```

Playbook will rotate `sshuser` account on LCM.

In case of playbook failure, log in to the `{{ locationCode }}lcm001` server via SSH and change the above users passwords manually to the one following the ATOS password policy. As the next step update the Hashi Vault entry for the given user.

### VMware Aria Operations for Networks Platform and Controller node accounts rotation

To perform Network Insight local account password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=vni
```

Playbook will rotate `admin@local`, `consoleuser` and `support` accounts on both Platform and Controller nodes from console.
After that it will update passwords stored in LCM.

To rotate passwords for VNI manually start with generating passwords that fits in ATOS password policy.

For `consoleuser` and `support` log in via ssh with consoleuser and change the passwords, once on vni host and once on vnc host. To do that use command:

```shell
modify-password system --user consoleuser
modify-password system --user support
```

For 'admin' login into the LCM with `vcfadmin@local` user and in the environments choose the VNI. From there use the dropdown option to `Change admin password` and continue with instructions found on screen.

Next step would be to log in to the `lcm001` with `vcfadmin@local` and creating in the locker entry for each of those passwords.

After finishing Trigger Inventory sync from `lcm001` for Network insight. LCM will ask you to provide the `consoleuser` and `support` users locker entries, provide ones that you created for vni with new password and finish the sync.

As the very last step update Hashi Vault entries for rotated users.

### c-kathos local user account rotation

To perform c-kathos local account password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=c-kathos
```

Playbook will rotate `c-kathos` accounts on Windows hosts.

To rotate passwords for c-kathos users manually start with generating passwords that fits in ATOS password policy.

Log in with ans01 user, and change locally passwords for the c-kathos users on each of the windows servers. After this action update the passwords in the Hashi Vault.

### Nessus local user account rotation

To perform Nessus local account password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=nessus
```

Playbook will rotate `nessus` accounts on nes001 host.

To rotate `nessus` account manually, generate passwords that will be fit with the ATOS password policy then log in to the nes001 host via ssh with privileged user.

For the `nessus` user use command:

```shell
/opt/nessus/sbin/nessuscli chpasswd "nessus"
```

After running the command provide the new password twice and confirm. When finished, update the Hashi Vault entry for `nessus` user.

### Edge transport nodes local user account rotation

To perform Edge transport nodes local account password rotation for all edges execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=edge
```

To do so for only particular edges use syntax as follows:

```shell
# For one Edge
ansible-playbook rotatePassword.yml -e executionOption=edge -e extraEdge=gre02ecn01edg04
# For list of Edges
ansible-playbook rotatePassword.yml -e executionOption=edge -e extraEdge=[gre02ecn01edg04,gre02ecn01edg05]
```

Playbook will rotate `admin`, `audit` and `root` accounts on both Edge transport node hosts.

To change the passwords manually, create passwords that will fit with ATOS password policy and log in to the edge node via ssh using root. Use below command command to change password for all `audit`, `admin` and `root`:

```shell
passwd audit
passwd admin
passwd root
```

Afterwards log in to the Hashi Vault and update entries for both of the accounts for each node requiring the change.

### Witness host local user account rotation

To perform Witness host local account password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=witness
```

Playbook will rotate `root` accounts on both witness hosts.

To change the passwords manually, create passwords that will fit with Atos password policy and log in to the witness host via ssh and use command line for `root` to change the password. Afterwards log in to the Hashi Vault and update entry for the witness root account in there.

> [!IMPORTANT]
> The witness Rotation is applicable only in the active-active type of sites.

### Schedule service account local user account rotation

To perform schedule service account password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=schedules
```

Playbook will rotate `svc-{{ locationCode }}-auto01`,`svc-{{ locationCode }}-auto02` and `svc-{{ locationCode }}-auto03` accounts on adc001 host.

To rotate each of above users manually, create passwords that will fit with ATOS password policy. Then log in to the adc001 server and from active directory change the passwords for those users. Afterwards go to the ansible server and log in with a root. Go to the `/home/svc-{{ locationCode }}-aut01/.schedulers` and check if the initialKey file is created in this directory. If yes update it with Base64 encoded new password and save it. If there is no file create it with root user and fill it with Base64 encoded new password.

After those actions update users passwords in the Hashi Vault.

### Linux local user account rotation

To perform Linux local account password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=linux
```

Playbook will rotate `root` and `next` accounts on linux hosts.

To update manually `root` and `next` users passwords, create passwords that will fit with ATOS password policy and log in via ssh to each of the linux hosts and change the passwords with commands:

```shell
passwd root
passwd next
```

Afterwards update each of the users entry in the Hashi Vault with the created password.

### AD Builtin Admin account rotation

To perform AD Builtin Admin account password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=adadmin
```

Playbook will rotate `administrator` account on adc001 host.

To rotate the `administrator` user manually, prepare password fit withe ATOS password policy. Afterwards log in to the adc001 and use AD to rotate the `administrator` user password.

When finished update the password in the Hashi Vault.

### SDDC Manager local accounts rotation

To perform SDDC Manager local accounts password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=sddc
```

Playbook will rotate `ansible`, `admin`, `vcf` and `root` account on SDDC manager.

To rotate manually each of SDDC Manager users prepare passwords fit with ATOS password policy. With such log in via ssh to the SDDC Manager and switch to the `root` user and continue.

Start with rotating `admin` password with command:

```shell
/opt/vmware/vcf/commonsvcs/scripts/auth/set-basicauth-password.sh admin {{ createdPassword }}
```

Then change of `vcf`, `root` and `ansible` user using the standard command:

```shell
passwd ansible
passwd root
passwd vcf
```

After successfully finishing the rotation, update all the passwords in the Hashi Vault.

### VMware Cloud Proxy (CPX) appliance local accounts rotation

To perform Cloud Proxy (CPX) local account password rotation execute:

```shell
ansible-playbook rotatePassword.yml -e executionOption=cpx
```

Playbook will rotate `admin` account on CPX.

To update manually `admin` user password, create password that will fit with Atos password policy and log in via ssh to cpx001 host and change the password with commands:

```shell
passwd admin
```

Afterwards update user entry in the Hashi Vault with the created password.

### HCX Appliance local accounts rotation

To perform HCX Appliance local account password rotation execute:

```shell
ansible-playbook resetHcxSystemAccounts.yml
```

Playbook will rotate `admin` and `root` accounts on HCX appliance (`{{ locationCode }}hcx001`).

To rotate passwords for HCX users manually, create passwords that fit with ATOS password policy.

For the `root` user, log in to the `{{ locationCode }}hcx001` server via SSH as `admin`, switch to root using the current root password, then change the password:

```shell
su root
passwd root
```

For the `admin` user, log in to the `{{ locationCode }}hcx001` server via SSH as `admin` and change the password:

```shell
passwd admin
```

After the rotation, update both the `admin` and `root` password entries in Hashi Vault under `servers/{{ locationCode }}hcx001`.

## Rotate passwords for service accounts

>Note: Most service accounts are rotated by scheduled playbook execution in crontab.

Service accounts are rotated by using `resetAdServiceAccount.yml` manage playbook.
Rotation for all service accounts at once can be executed by running below command.

```shell
ansible-playbook resetAdServiceAccount.yml -e '{"srvAccountName":"{{ automationServiceAccounts + otherServiceAccounts }}"}'
```

Playbook will rotate passwords and update integrations - registering any errors found in report. Once playbook run is finished rotation status can be confirmed by reading `svcAccountRotation` report generated at home directory of executing user. If all `AD Rotation Status` fields read `SUCCESS` or `NOT FOUND` it means password change on AD was successful. `Integrations Update Status` field lists all integrations that were updated and highlights any failed updates - these need to be fixed manually by following steps for respective account.

In case rotation is to be executed for a single account, run playbook with added extra var.

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-<locationCode>-<serviceAccountCode>'
```

To skip password reset and only update integrations for the accounts, the playbook can be executed with a tag `integrationsOnly`.

```shell
ansible-playbook resetAdServiceAccount.yml -t integrationsOnly
```

Integrations of a specific account can be updated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-<locationCode>-<serviceAccountCode>' -t integrationsOnly
```

### SoxDB automation credentials update

In case password rotation occured for `svc-< locationCode >-ans0x` account make sure to reflect new credentials for existing SoxDB integration.

The following procedure describes steps how to update inside Ansible controller machine ans account credentials.

Step 1. Logon into ans001 machine using next account and switch into root user

Step 2. Go to location /root/.secureFiles

Step 3. Decrypt file "file.enc" using base64 (requires multiple base64 decryption attemps to see finally content)

Step 4. Update fileds ansPassword or userPassword with new credentials

Step 5. Save file and encrypt file using base64 (same amount of encryption attempts like for decryption)

### Service accounts without automation integrations

Following accounts don't have any automation dependencies and can be rotated at any time:

- `svc-< locationCode >-bil01`
- `svc-< locationCode >-lcm01`
- `svc-< locationCode >-vlt02`
- `svc-< locationCode >-vro01`
- `svc-< locationCode >-vra01`

They are rotated by the playbook by default, if no extra vars are specified:

```shell
ansible-playbook resetAdServiceAccount.yml
```

Playbook will rotate passwords and update integrations - registering any errors found in report. Once playbook run is finished rotation status can be confirmed by reading `svcAccountRotation` report generated at home directory of executing user. If all `AD Rotation Status` fields read `SUCCESS` or `NOT FOUND` it means password change on AD was successful.

Alternatively they can be rotated one by one by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-bil01'
```

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-lcm01'
```

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-vlt02'
```

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-vro01'
```

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-vra01'
```

### Service accounts with automation integrations

Following accounts have automation dependencies that are updated automatically by playbook.

- `svc-< locationCode >-ans01`
- `svc-< locationCode >-ans02`
- `svc-< locationCode >-ans03`
- `svc-< locationCode >-vlt01`
- `svc-< locationCode >-vcs01`
- `svc-< locationCode >-vcs02`
- `svc-< locationCode >-vcs03`
- `svc-< locationCode >-hgw01`
- `svc-< locationCode >-vni01`
- `svc-< locationCode >-bck01`
- `svc-< locationCode >-paladin`
- `svc-< locationCode >-srm01`
- `svc-< locationCode >-vli01`
- `svc-< locationCode >-vcf01`
- `svc-< locationCode >-hcx01`

They can be rotated all at once by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName={{ automationServiceAccounts }}'
```

Playbook will rotate passwords and update integrations - registering any errors found in report. Once playbook run is finished rotation status can be confirmed by reading `svcAccountRotation` report generated at home directory of executing user. If all `AD Rotation Status` fields read `SUCCESS` or `NOT FOUND` it means password change on AD was successful. `Integrations Update Status` field lists all integrations that were updated and highlights any failed updates - these need to be fixed manually by following steps for respective account.

### Ansible service account - Windows

This service account is used for communication with Windows VMs.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-ans01'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### vROps Network Share Plugin

- Login to vROps WEB UI `https://< vropsServerFQDN >/` using your AD user credentials.
- Navigate to `Operations->Configurations->Outbound settings`.
- Click on `Edit` in menu of `vROPs_Compliance - Network Share Plugin`.
- Enter the newly changed `svc-< locationCode >-ans01` user password.
- Click `TEST` to verify connection.
- Click `SAVE`.

#### Task Scheduler on TSS002

- Login to TSS002 and open Task Scheduler
- Update password for all tasks using ans01 account, so far following tasks are used:
  - vROpsMapper
  - UploadITCReportsUsingCurl
  - PP Check - Weekly Alcatraz Reports Upload Check
  - ArchiveOldReportsAfter24hrs  
  - CAPM
  - CustUsersReport
  - ResetADPasswords
  - UserPasswordExpiryNotification
  - Oradad_mail
  - Oradad_scan

>Note: not all scheduler tasks have to be present on given VCS environment.

#### Nessus scan with credentials for windows

Run `configureNessusCredScan.yml` playbook to update credentials automatically. If the playbook fails follow manual steps:

- Login to Nessus using `nessus` account at `https://< locationCode >nes001.< domain >:8834/`
- Open scan by clicking on it, on next page click `Configure` button
- Select `Credentials` tab and if there are no credentials configured (SSH and Windows) click on left in the `Categories` tab then in the dropdown choose `Host` category to add them
- Configure Windows credentials as follows:
  - Authentication method: `Password`
  - Username: `svc-< locationCode >-ans01@< domain >`
  - Password: Account password from Vault
- Leave other settings at default and click `Save` button at the bottom

### Ansible service account - Linux

This service account is used for communication with Linux VMs.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-ans02'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### Nessus scan with credentials for linux

Run `configureNessusCredScan.yml` playbook to update credentials automatically. If the playbook fails, follow manual steps:

- Login to nessus using nessus account at `https://< locationCode >nes001.< domain >:8834/`
- Open scan by clicking on it, on next page click `Configure` button
- Select `Credentials` tab and if there are no credentials configured (SSH and Windows) click on left in the `Categories` tab then in the dropdown choose `Host` category to add them
- Configure SSH Credentials as follows:
  - Authentication method: `password`
  - Username: `svc-< locationCode >-ans02@< domain >`
  - Password: Account password from Vault
  - Elevate privileges with: `sudo`
- Leave other settings at default and click `Save` button at the bottom

### Ansible service account - Hashi Vault

This service account is used for Hashi Vault access.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-ans03'
```

When running this playbook for the Siemens customer there will be additional prompt for FTH domain credentials to connect to vRA.

For the Siemens customer vRO configuration update is automated - manual steps should be followed only in case of playbook failure.
Status of vRO workflow is reported at the end of playbook run - if it reports failed or running status it needs to be investigated.
vRA Cloud Secret password update is automated.
For all other VCS customers using SSR's follow generic VCS manual steps.\
**NOTE:** For AVIVA customer update of vRO configuration (SSRConfig) is automated (resetAdServiceAccount.yml). However "Save" of the updated SSRConfig has to be done manually (newly introduced API limitation):

- Login to vRA `https://< vra001Fqdn >/` using your AD credentials and go to `Orchestrator`.
- Go to `Configurations -> SSRConfig`.
- Click **Save** to save configuration file.

#### Orchestrator configuration update

Siemens VCS steps:

- Login to Vault and read password for `svc-< locationCode >-ans03` - entry under path  
  `secret/< customerCode >/< locationCode >/activedirectory/svc-< locationCode >-ans03`
- Login to VMware vRealize Orchestrator WEB UI `https://< vro001ProxyFqdn >/` using your AD credentials.
- Go to `Library -> Workflows`.
- Find `dhcUpdateHashiServiceAccount` workflow and `RUN` it.
- In `newPassword` field put `ans03` account password read earlier.
- Click `RUN`.
- Verify if workflow run finished successfully.

vRA Cloud Secret password update procedure for Siemens VCS:

- Login to `<locationCode>vra001` using AD credentials.
- Navigate to **Infrastructure** -> **Secrets**.
- Click on secret named: `svc-< locationCode >-ans03`.
- Provide new password (rotated one / changed) in **Value** and click **Save**.

Generic VCS steps:

- Login to Vault and read password for `svc-< locationCode >-ans03` - entry under path  
  `secret/< customerCode >/< locationCode >/activedirectory/svc-< locationCode >-ans03`
- Login to vRA `https://< vra001Fqdn >/` using your AD credentials and go to `Orchestrator`.
- Go to `Configurations -> SSRConfig`.
- Edit configuration file, go to variables and update variable named **vaultPassword**.  Click on it and provide new password (rotated one / changed).
- Click **Save** to save the variable.
- Click **Save** again to save configuration file.

Proceed with below steps for DR enabled sites only:
>Note: Skip this step for Siemens VCS since it's covered by `dhcUpdateHashiServiceAccount` Workflow ran in previous step.

- Login to vRA `https://< vra001Fqdn >/` using your AD credentials and go to `Orchestrator`.
- Go to `Configurations -> Library -> DR -> drConfigurationFile`.
- Edit configuration file, go to variables and update variable named **vaultPassword**.  Click on it and provide new password (rotated one / changed).
- Click **Save** to save the variable.
- Click **Save** again to save configuration file.

### Hashi Vault bindpass account

This service account is used for binding AD domain to Hashi Vault allowing login with user domain credentials.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-vlt01'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### Vault bindpass

Verify if login with AD credentials to Vault is possible.
If not follow below steps:

- Login to Vault using `Token` method, in token field provide root token acquired from DevSecOps Team Lead.
- Collect `svc-<locationCode>-vlt01` account password from Vault.
- Go to `Access` Vault menu, then click on `ldap` option.
- Click `Configuration` and on next screen `Configure`.
- Open `Customize User Search` dropdown.
- Update `Bindpass` field with `svc-<locationCode>-vlt01` user password and click `Save`.
- Verify if AD login is working.

### vCenter service account

This service account is used for communication with vCenter.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-vcs01'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### vCenter AD identity provider

Verify if login with AD credentials to vCenter is possible. If not follow below steps:

- Login to vCenter with `administrator@vsphere.local` user account.
- Go to Menu and `Administration`.
- In `Single Sign On` go to `Configuration`.
- In Active directory check whether VCS domain can found.
- In identity sources click on the domain and then `EDIT`.
- Provide the `svc-< locationCode >-vcs01` AD service account credentials in `Password` field.
- In `Certificates` field provide crt files for `adc001` and `adc002`, generate them using below steps if needed.
  - Open SSH session to vCenter.
  - Run following commands - make sure to replace `<locationCode>` and `<domain>` with proper data:

    ```shell
    openssl s_client -connect <locationCode>adc001.<domain>:636 -showcerts
    openssl s_client -connect <locationCode>adc002.<domain>:636 -showcerts
    ```

  - Copy first certificate from output (starting from first `BEGIN CERTIFICATE` line in output, ending at first `END CERTIFICATE` line found).
  - Save copied output as `<locationCode>adc001.crt` and `<locationCode>adc002.crt` respectively.
- Once certificates are added click `SAVE` to update Identity Source.
- Verify if AD login to vCenter is possible now.

#### Task Scheduler on TSS002

- Login to TSS002 and open Task Scheduler
- Update password for all tasks using vcs01 account, so far following tasks are used:
  - VM Storage Policy
  - vSANReport
  - ClusterUsage

>Note: Not all scheduler tasks have to be present on given VCS environment

#### vROps Adapters

- Log in to vROPS instance.
- Go to `Administration -> Integrations` menu on left side.
- Check if vCenter adapters are showing `OK` Status.
- If any adapter is not working, follow below steps to update manually:
  - Click on adapter menu and then `Edit`.
  - Click crayon button next to `Credential` field.
  - Verify if `User name` field has `svc-< locationCode >-vcs01@< domain >`.
  - Put new password in `Password` field and click `OK`.
  - Click `VALIDATE CONNECTION` and `ACCEPT` certificate if prompted.
  - After successful validation click `SAVE` button

### vCenter administrator service account

This service account is used for communication with vCenter with administrator rights.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-vcs02'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

> [!Important]
> In Siemens VCS environments a custom account `svc-{{ locationCode }}-vcs02-siem` is used instead and it **must not** be rotated with this process.

#### vRO vCenter plugins

For VCS environments using vRA on-prem:

- Login to `https://< vra001ProxyFqdn >/` VM using AD account.
- Go to `Orchestrator`.
- Go to `Library -> Workflows`, then find `Update a vCenter Server instance` workflow.
- Run the workflow with following details:
  - Will you orchestrate this host? - checked
  - Ignore certificate warnings? - checked
  - Do you want to create a session per user to the vCenter Server? - unchecked
  - Username - `svc-< locationCode >-vcs02@< domainName >`
  - Password - password for account from Vault
- Repeat the step for second vCenter in environment if needed.

#### vRA on-prem vCenter Cloud Account

>Note: These steps apply only to VCS environments using vRA on prem.

- Login to `<locationCode>vra001`.
- Go to `Assembler -> Infrastructure -> Cloud Accounts`.
- Check if `<locationCode>vcs002` cloud account has `OK` status.
- Update the account with `Warning` status by following below steps:
  - Click on cloud account name.
  - In `Credentials` section update `Password` field.
  - Click `Validate`.
  - When validation is successful click `SAVE`.
- Repeat above steps for all tenants present (`<tenant>.<locationCode>vra001`).

### vCenter read-only service account

This service account is used for read-only access to vCenter.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-vcs03'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### RVTools script on TSS002

- Open `D:\Scripts\CAPM\RVtool\Config.xml` file in text editor and note down `username` field.
- Open PowerShell as `svc-< locationCode >-ans01@< domainName >` user.
- Run RVTools password encryption script found in `C:\Program Files (x86)\Dell\RVTools\RVToolsPasswordEncryption.ps1` by default.
- Encrypt password for noted down account.
- Check if value created matches `EncryptedPassword` field in `Config.xml`.
- If values don't match update `Config.xml` with new value and save file.

### VCF service account

This service account is used for communication with SDDC Manager.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-vcf01'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### vROps Adapter

- Log in to vROPS instance.
- Go to `Administration -> Integrations` menu on left side.
- Check if VCF adapter is showing `OK` Status.
- If the adapter is not working, follow below steps to update manually:
  - Click on adapter menu and then `Edit`.
  - Click crayon button next to `Credential` field.
  - Verify if `User name` field has `svc-< locationCode >-vcf01@< domain >`.
  - Put new password in `Password` field and click `OK`.
  - Click `VALIDATE CONNECTION` and `ACCEPT` certificate if prompted.
  - After successful validation click `SAVE` button

### HTTP Gateway service account

This service account is used for communication between HGW script and vCenter.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-hgw01'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### HGW script

- SSH to `<locationCode>hgw001` VM.
- Open `/opt/pubsubpy3.7/dpcop-pubsub-http-gw-master/pubsubhttpgateway/opt/pubsubpy3.7/dpcop-pubsub-http-gw-master/pubsubhttpgateway/entrypoint.sh` file.
- Take value from `VCENTER_CRED` field, decode it with base64.
- Compare decoded value with `svc-<locationCode>-hgw01` service account password in vault - they should match.
- If passwords don't match, base64 encode (with padding) password from vault.
- Put new encoded password in place of previous one in the file, then save the file.
- Run following command on `<locationCode>hgw001` VM to apply the change:

   ```shell
   ps -ef | grep 'unicorn' | grep -v grep | awk '{print $2}' | xargs -r kill -9
   ```

>Note:
>On VCS environments using Ubuntu 22.04 script location is `/opt/pubsubhttpgatewayentrypoint.sh` instead.

### vRNI service account

This service account is used for communication between Network Insight and vCenter.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-vni01'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### vRNI vCenter Data Sources

Check the vRealize Network Insight to see if the password for the `svc-< locationCode >-vni01@< searchDomain >` user been updated for vCenter Data Sources.
To check, login as AD user to vRNI and go to `Settings/Accounts and Data Sources`.
If there is an issue with data collection, update vCenter Data Sources with the new password by following the steps below:

- Select the vCenter Data Sources for which password will be updated. Click pencil icon to edit.
- Provide the new password and click `VALIDATE` and `SUBMIT`.

### Backup service account

This service account is used for file based backup on management stack.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-bck01'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### SDDC manager backup

- Login to SDDC Manager with AD credentials
- Navigate to `Administration->Backup->Site Settings`
- Click on `EDIT` button and fill out configuration as follows:
  - Host FQDN or IP - IP of `<locationCode>ans001` VM
  - Port - `22`
  - Transfer Protocol - `SFTP`
  - Username - `svc-< locationCode >-bck01`
  - Password - password for service account
  - Backup Directory - `/backup/vcf`
  - SSH Fingerprint - should auto-fill after providing IP and Port, if they were not edited click them so SDDC will query for fingerprint
  - Confirm Fingerprint - checked after confirming SSH fingerprint matches
  - Encryption Passphrase - `backupVcfPassphrase` from `<locationCode>ans001` VM Vault entry
- Click `SAVE`

#### VCSA backup

- Login to VMware vCenter Server Management page (`https://<vCenterFQDN>:5480/`) with administrator credentials
- Navigate to `BACKUP` page
- Click on `EDIT` button and fill out configuration as follows:
  - Backup location - `sftp://< locationCode >ans001.< domain >/backup/vcsa`
  - Backup server credentials
    - User name - `svc-< locationCode >-bck01`
    - Password - password for service account
  - Schedule - Custom, Monday to Saturday at 11 PM
  - Encryption Password - `backupVcfPassphrase` from `<locationCode>ans001` VM Vault entry
- Click `SAVE`
- Repeat this for all vCenter servers in environment

### Customer backup service account (BTN only)

This service account is used for Networker backup on management stack in BTN.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-bck02'
```

The user executing the playbook is prompted to specify the vCenter instance for which the password should be updated. As the backup is configured for the management stack, please enter `vcs001`.

All of this account's automation dependencies are updated automatically by the playbook.

After running the playbook the password needs to be sent to the Networker backup team via an encrypted e-mail. The team needs to update their vault after such password rotation. If the playbook fails at any point, please also ask them to update the password in the Networker tool for the vCenter server.

The list of emails for Networker Backup Team (BTN customer):

| Email                                  |
|----------------------------------------|
| `barry.beckers@atos.net`               |
| `karel.bos@atos.net`                   |
| `selwyn.rebello@atos.net`              |
| `dlnlo-storage-cbs-networker@atos.net` |

### Paladin service account

This service account is used for communication from Paladin external tool to vROPS.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-paladin'
```

After rotating this account Paladin tool has to be updated manually by following below steps:

#### Prepare cURL request

Securely extract the new password for Paladin service account from HashiCorp Vault active directory folder.

Prepare the cURL request by replacing `<SecurePassword>` placeholder with the new password from vault and the other parameters in brackets with values for current location (according to below table):

cURL message model:

```shell
  curl -XPOST -H "Content-type: application/json" -d '{
      "name": "<Location_Name>",
      "base_url": "<Base_URL>",
      "credentials": {
          "username": "<Username>",
          "password": "<SecurePassword>",
          "authSource": "<AuthSource>"
      }
  }' 'https://siemens-api.admin.bgi.atos-srv.net/vrops_credentials'
```

| LocationName | BaseURL                   | Username                          | Password        | AuthSource     |
|--------------|---------------------------|-----------------------------------|-----------------|----------------|
| Barueri      | bre01ops001.siedhc11.next | `svc-bre01-paladin@siedhc11.next` | SecuredPassword | vIDMAuthSource |
| Berlin       | bln01ops001.siedhc05.next | `svc-bln01-paladin@siedhc05.next` | SecuredPassword | vIDMAuthSource |
| Braunschweig | bwg01ops001.siedhc04.next | `svc-bwg01-paladin@siedhc04.next` | SecuredPassword | vIDMAuthSource |
| Feucht       | fth01ops001.siedhc02.next | `svc-fth01-paladin@siedhc02.next` | SecuredPassword | vIDM           |
| Feucht-CAT   | fth01ops001.siedhc01.next | `svc-fth01-paladin@siedhc01.next` | SecuredPassword | vIDM           |
| Irving       | irv01ops001.siedhc06.next | `svc-irv01-paladin@siedhc06.next` | SecuredPassword | vIDMAuthSource |
| Karlsruhe    | khe01ops001.siedhc03.next | `svc-khe01-paladin@siedhc03.next` | SecuredPassword | vIDMAuthSource |
| Kista        | bmm01ops001.siedhc07.next | `svc-bmm01-paladin@siedhc07.next` | SecuredPassword | vIDMAuthSource |
| Lupfig       | lpg01ops001.siedhc10.next | `svc-lpg01-paladin@siedhc10.next` | SecuredPassword | vIDMAuthSource |
| Madrid       | mad01ops001.siedhc09.next | `svc-mad01-paladin@siedhc09.next` | SecuredPassword | vIDMAuthSource |
| Vienn        | vie01ops001.siedhc08.next | `svc-vie01-paladin@siedhc08.next` | SecuredPassword | vIDMAuthSource |

#### Send request from MobaXterm terminal session

Log into the Saacon TSS using appropriate credentials. Launch MobaXterm app and Start local terminal session.

Execute the prepared cURL request in the MobaXterm local session for current site.

Log the status of cURL request securely for audit purposes.

#### Notification

After successfully completing the cURL request, send a notification email to relevant stakeholders:
Technical-Bridge-Support <technical-bridge-support@atos.net>; dl-bridge-tooling-project-support <dl-bridge-tooling-project-support@atos.net>; Dipesh Chauhan <dipesh-kumar.chauhan@atos.net>.

### SRM service account

The service account is intended for accessing SRM (as well as via the GUI), and is also used for communication between the local vRO (SRM plugin) and local and remote VCS locations.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-srm01'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps:

- Login to VMware vRealize Orchestrator WEB UI using your VMware vRA credentials:
  - `https://< abx001ProxyFqdn >/` for VCS using vRA Cloud
  - `https://< vro001ProxyFqdn >/` for VCS using standalone vRO
  - `https://< vra001ProxyFqdn >/` for VCS using embedded vRO (go to `Orchestrator` service once logged in)
- Go to Inventory and try to expand SRM – if the configuration update is required, SRM inventory will be empty.

To reconfigure SRM inventory:

- Go to `Workflows -> Library -> SRM -> Configuration -> Remove Local Sites` and click RUN.
- On the next screen click RUN.
- Click Close and click `Configure Local Sites Workflow` -> ALL RUNS.
- Select one of the recent successfully completed workflows from the list and click Run again.
- On the next screen provide `svc-< locationCode >-srm01@< domainName >` password from Hashicorp Vault (stored under activedirectory\svc-< locationCode >-srm01) and click RUN.
- After successfully running the workflow, click Close, select `Configure Remote Site` Workflow -> ALL RUNS.
- Select one of the recent successfully completed workflows from the list and click Run again.
- On the next screen click RUN.
- After successfully running the workflow, click Close, select `Login Remote Site Workflow` -> ALL RUNS
- Select one of the recent successfully completed workflows from the list and click Run again.
- In the Password field on the next screen, provide `svc-< remoteLocationCode >-srm01@< remoteDomainName >` password from Hashicorp Vault from the remote/DR site (under activedirectory\svc-< remoteLocationCode >-srm01) and click RUN.
- After running successfully, close the workflow Window. Go to the Administration -> Inventory -> SRM and check if you can browse SRM Inventory.
- Please also double check if vSphere Replication Inventory is visible.
- If not, please double check if the vCenter Server Plug-ins are configured correctly in the inventory.
- If not – update credentials for both vCenter Servers using `Update a vCenter Server instance` workflow. You can also rerun some recent workflows.
- After updating credentials for both vCenter Servers in the Inventory, vSphere Replication inventory should be visible again.
- Under Assets -> Configurations -> drConfigurationFile -> Open you can verify if DR configuration was populated correctly.

Additionally, new account password should be saved on remote site Vault:

- Login to remote Vault using `svc-< remoteLocationCode >-ans03@< remoteDomainName >` account.
- Go to `activedirectory` folder and find `svc-< locationCode >-srm01@< domainName >` entry.
- Update the entry with password taken from local Vault.

### IDM AD directory bind account

This service account is used for binding AD domain to IDM.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-idm01'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### IDM bindpass

Verify if AD directory sync is working on IDM.
If not follow below steps:

- Login to IDM using `administrator` user from System Domain.
- Go to `Identity & Access Management` tab.
- Open directory named the same as DHC AD domain by clicking on its name.
- In `Bind User Details` section put credentials for `svc-<locationCode>-idm01` service account.
- Click `Save` button and wait for configuration to be completed.
- Use `Sync Now` button to verify bind update was successful.
- Repeat above steps for all existing tenants (IDM for each tenant is accessible under `< tenantName >.< domainName >`).

### Aria Operations for Logs service account

This service account is used for log collection from Aria Operations for Logs appliance from vCenter servers.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-vli01'
```

All of this account's automation dependencies are updated automatically by the playbook.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

#### Aria Operations for Logs vSphere integrations

Update service account password in case automation failed:

- Login to Aria Operations for Logs using `admin` user.
- Go to `Integrations -> vSphere` page.
- Open menu on the left side of integration and click `Edit`.
- On edit screen provide credentials for `svc-<locationCode>-vli01` account.
- Click `TEST CONNECTION` to verify login details.
- Click `SAVE` to update integration details.
- Repeat the steps for any remaining vSphere integrations.

### HCX service account

This service account is used for communication between HCX and vCenter/NSX Manager.

It can be rotated by running:

```shell
ansible-playbook resetAdServiceAccount.yml -e 'srvAccountName=svc-{{ locationCode }}-hcx01'
```

The playbook automatically updates vCenter Server and NSX Manager configurations in HCX with the new credentials.
However if the playbook fails at any point, verify them manually and update if needed by following below steps.

> [!IMPORTANT]
> After rotating this account, you must manually update the credentials on any active HCX connections between all paired sites.

#### HCX vCenter Server configuration

- Login to the HCX Manager Admin UI at `https://{{ locationCode }}hcx001.<domain>:9443` using hcx admin.
- Verify if the vCenter connection shows green dot status in Dashboard.
- Go to `Configuration -> vCenter Server`.
- If not, click `Edit` and update the credentials with the new `svc-{{ locationCode }}-hcx01` account password.
- Click `Save` to apply the changes.

#### HCX NSX Manager configuration

- Login to the HCX Manager Admin UI at `https://{{ locationCode }}hcx001.<domain>:9443` using hcx admin
- Verify if the NSX Manager connection shows green dot status in Dashboard.
- Go to `Configuration -> NSX Manager`.
- If not, click `Edit` and update the credentials with the new `svc-{{ locationCode }}-hcx01` account password.
- Click `Save` to apply the changes.

## Rotate LCM002 components manually (Siemens FTH and CAT only)

On Siemens FTH and CAT sites following passwords need to be rotated manually:

- VMware Aria Automation (vra001)
- VMware Aria Automation Orchestrator (vro001)
- VMware Identity Manager (idm005)
- VMware Aria Suite Lifecycle (lcm002)

All below steps are executed on additional `<locationCode>lcm002` appliance on FTH/CAT.

### Rotate passwords for VMware Aria Automation

In order to change a vRA `root` password execute following steps:

1. Log in to vRSLCM and navigate to *Locker* -> *Passwords*.
2. Click *ADD* button on top.
3. Please fill in password details as follows:

     - **Password Alias**: `vraRootPassword_{date}` (i.e. `vraRootPassword_19Jul2023`)
     - **Password**: type new password here
     - **Confirm Password**: type new password here
     - **Password Description**: `vRA root password`
     - **Username**: `root`

4. Navigate to *Lifecycle Operations* -> *Environments*. Find the vRA environment and click *View Details*.
5. Click on *VMware Aria Automation Nodes* and then select primary node (vra002). Click *Change Node Password*.
6. Select proper **Current Password** and **New Password** and click *Submit*.
7. A request for password change will start.
8. Wait until the request is finished. Normally it is a very quick task consuming about 5 seconds.
9. Repeat steps 5-8 with remaining two secondary nodes one by one (vra003 and vra004).
10. Update password in HashiVault.
11. vRA rotation password is completed.

### Rotate passwords for VMware Aria Automation Orchestrator

In order to change a vRO `root` password execute the following steps:

1. Log in to vRSLCM and navigate to *Locker* -> *Passwords*.
2. Click *ADD* button on top.
3. Please fill in password details as follows:

      - **Password Alias**: `vroRootPassword_{date}` (i.e. `vroRootPassword_19Jul2023`)
      - **Password**: type new password here
      - **Confirm Password**: type new password here
      - **Password Description**: `vRO root password`
      - **Username**: `root`

4. Navigate to *Lifecycle Operations* -> *Environments*. Find the vRO environment and click *View Details*.
5. Click on *Change Root Password*.
6. Select proper **Current Password** and **New Password** and click *Submit*.
7. A request for password change will start.
8. Wait until request is finished.
9. Update password in HashiVault.
10. There is no need to change password for each vRO node - LCM is changing password only for primary node and sync it with secondary nodes.
11. vRO rotation password is completed.

Above steps need to be repeated for all vRO appliances attached to LCM.
Passwords for remote vRO nodes should be updated on Hashi Vault in the same location as that node.

### Rotate passwords for VMware Identity Manager

In order to change a vIDM `root` password execute the following steps:

1. Log in to vRSLCM and navigate to *Locker* -> *Passwords*.
2. Click *ADD* button on top.
3. Please fill in password details as follows:

      - **Password Alias**: `vidmRootPassword_{date}` (i.e. `vidmRootPassword_19Jul2023`)
      - **Password**: type new password here
      - **Confirm Password**: type new password here
      - **Password Description**: `vIDM root password`
      - **Username**: `root`

4. Navigate to *Lifecycle Operations* -> *Environments*. Find the vIDM environment and click *View Details*.
5. Click on *VMware Identity Manager Primary Node* and then select primary node (idm005). Click *Change Node Password*.
6. Select proper **Current Password** and **New Password** and click *Submit*.
7. A request for password change will start.
8. Wait until request is finished.
9. Update password in HashiVault.
10. vIDM rotation for `root` password is completed.

In order to change a vIDM `administrator` password execute the following steps:

1. Log in to vRSLCM and navigate to *Locker* -> *Passwords*.
2. Click *ADD* button on top.
3. Please fill in password details as follows:

      - **Password Alias**: `vidmAdminPassword_{date}` (i.e. `vidmAdminPassword_19Jul2023`)
      - **Password**: type new password here
      - **Confirm Password**: type new password here
      - **Password Description**: `vIDM admin password`
      - **Username**: `administrator`

4. Navigate to *Lifecycle Operations* -> *Environments*. Find the vIDM environment and click *View Details*.
5. Click on *Change Admin Password*.
6. Select proper **Current Password** and **New Password** and click *Submit*.
7. A request for password change will start.
8. Wait until request is finished.
9. Update password in HashiVault.
10. LCM rotation for `administrator` password is completed.

### Rotate passwords for VMware Aria Suite Lifecycle

In order to change an LCM `admin@local` password execute the following steps:

1. Log in to vRSLCM and navigate to *Lifecycle Operations* -> *Settings* -> *Change Password*.
2. Type new password twice and click *Change*.
3. After successful admin password change, you will be redirected to login page where re-login is required. Please do so (with new password) and confirm that new password is working.
4. Update password in HashiVault.
5. LCM rotation for `admin@local` password is completed.

In order to change an LCM `root` password execute the following steps:

1. Log in to vRSLCM and navigate to *Locker* -> *Passwords*.
2. Click *ADD* button on top.
3. Please fill in password details as follows:

      - **Password Alias**: `lcmRootPassword_{date}` (i.e. `lcmRootPassword_19Jul2023`)
      - **Password**: type new password here
      - **Confirm Password**: type new password here
      - **Password Description**: `LCM root password`
      - **Username**: `root`

4. Navigate to *Lifecycle Operations* -> *Settings* -> *Change Password*.
5. Select proper **Current Root Password** and **New Password** and click *Change*.
6. A request for password change will start. Click link inside green popup information.
7. Wait until request will be finished.
8. Update password in HashiVault.
9. LCM rotation for `root` password is completed.

## Create cross site Hashi Vault backup

<b>Please follow the guidelines below if the client has two VCS locations (customer code is the same for both sites). If not, you can skip the below steps.</b>

- After every password rotation, use playbook <b>(crosssiteHashiVaultBackup.yml)</b> to export the credentials from source VCS and import that to another VCS instance for backup purpose.
- Run playbook on source site's ansible server - ans001. Path <b> /opt/dhc/manage </b><br>```ansible-playbook crosssiteHashiVaultBackup.yml```
- Repeat this playbook execution step on destination site ansible server to backup credentials from destination VCS HashiVault to source VCS HashiVault.
- For more information follow document- [wiBackupHashivaultCredentials.md](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/workInstructions/wiBackupHashivaultCredentials.md)

### Scheduling cross site Vault backup using crontab

This step can be scheduled to run automatically using crontab.

1. Ensure aut06 certificate is present and valid:

    ```shell
    ansible-playbook updateAutServiceAccountsCertificates.yml --tags certAut06
    ```

2. Schedule the playbook execution:

   For DR paired sites:

   ```shell
   ansible-playbook configurePasswordRotationCron.yml -t hashivaultBackup
   ```

   For backing up to other sites:

   ```shell
   ansible-playbook configurePasswordRotationCron.yml -t hashivaultBackup -e "locationCodeDr=<remoteLocationCode> customerCodeDr=<remoteCustomerCode> targetSitevaultIp=<remoteVaultIP> remoteDomainName=<remoteDomainName>"
   ```

This job will be executed on password rotation day as last task.

## Export key passwords to CyberArk safe

In case previous step was skipped (due to lack of DR paired site), key passwords will have to be exported to CyberArk safe.

1. Execute following playbook:

    ```shell
    ansible-playbook exportCredentialsCyberArk.yml
    ```

2. Collect `passwordExport` csv file from executing user home directory.
3. Log in to CyberArk.
4. Check if accounts for selected VCS exist in the safe - if they do follow these steps to remove them before uploading new versions:

    - On left menu click `Accounts -> Accounts & Requests -> Accounts View (Classic UI)`
    - Search for domain of selected VCS using bar on the right.
    - Select any accounts that will be uploaded.
    - Click `Modify -> Delete` and confirm deletion.
    - Go back to `Accounts -> Accounts & Requests -> Accounts View` and proceed with next steps.

5. Under `Add account` button select `Add accounts from file`.
6. Open collected file and click `Upload`.
7. Verify import result to make sure all accounts were imported correctly.
8. Remove any copies of `passwordExport` csv file from local machine and ansible directory.

## Collect evidence

Once password rotation is finished it is required to collect evidence report by running below command.

```shell
ansible-playbook getPasswordExpiration.yml
```

The report created by this playbook can be found under running users home folder on `<locationCode>ans001` VM - file name starts with `expirePassList`.
This file has to be attached to password rotation change ticket as evidence of successful implementation.
