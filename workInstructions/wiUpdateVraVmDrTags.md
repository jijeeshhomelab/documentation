# List of Changes
  
| Version | Date       | Description      | Author       |
| ------- | ---------- | ---------------- | -------------|
| 0.1     | 16.02.2023 | First version    | Lukasz Tomaszewski |
| 1.0     | 24.05.2023 | Doc renamed and adjusted to work with vRA cloud and on-prem | Robert Kaminski |

## Introduction

The onboarding playbook relies on two tags, UHC-SN-DR-PROTECTION-GROUP and UHC-SN-DISASTER_RECOVERY. VM tags might be missing after the failover or be inaccurate, i.e. VMs were manually reassigned to other protection groups, VMs were moved to other storage clusters which automatically changes the protection group assignment or protection group were renamed.

It is strongly recommended to follow this procedure to apply the correct tags.

### Purpose

Update vRA VM tags that are required for 3rd step of A/P DR failover, vRA onboarding, especially for VMFS on FC.

### Audience

- VCS Operations
- VCS Engineering

### Scope

The scope of this document covers the following:

1. Export recovery plan from Site Recovery Manager
2. Execute Ansible playbook to update DR related tags on vRA VMs.

# Related Documents

| Document |
| -------- |
| [A/P DR failover work instruction](wiFailoverActivePassiveDr.md)|

# Prerequisite

Either vRA Cloud or vRA On-prem requires valid user token in order to connect to it via code. Address it before executing an update playbook.

- vRA Cloud token

  As a prerequisite a valid token is required to establish a connection to vRA Cloud. The token is stored in HashiCorp Vault (`secret/<customerCode>/<locationCode>/vracloud/<customerCode>/authorizationToken-<dasId>`) and is valid for 1 day. To create the Token run a playbook:

  ```shell
   ansible-playbook createVraCloudToken.yml
  ```

   You will be prompted to provide the following inputs:

     ```text
     Enter VCS management domain username e.g.: a123456@exampledomain.com
     Your input: Your Active Directory domain username

     Enter the password for the user domain. Please note that the password you enter will not be displayed on the screen. 
     Your input: Your Active Directory domain password

     Enter vRA Cloud username e.g.: a123456@exampledomain.com 
     Your input: Your e-mail address associated with VMware Cloud Services

     Enter password for vRA Cloud user 
     Your input: Your password for the above account

     Please enter the name of the tenant organization, i.e. nx302
     Your input: tenant organization name

     NOTE: AS THIS IS SELENIUM, BEFORE ENTERING PIN, PLEASE WAIT FOR NEW VALIDITY CYCLE

     Please enter MFA Authenticator PIN
     Your input: PIN
     ```

     >Note: During the execution of this playbook you will be prompted for One Time Password (OTP) used in Multi Factor Authentication (MFA). Without enabled MFA on the vRA account, the playbook will fail.

- vRA On-prem token

   A token is also required in order to establish connection to vRA On-prem. The token is stored in HashiCorp Vault (`secret/<customerCode>/<locationCode>/vraonprem/<customerCode>/authorizationToken-<dasId>`) and is valid for 1 day. To create the Token run a playbook:

   ```shell
     ansible-playbook createVraOnPremAuthToken.yml
   ```

# Update procedure

## Export recovery plan from Site Recovery Manager (SRM)

- Login to vCenter Server (DR site does not matter), select `Menu` -> `Site Recovery` -> `OPEN Site Recovery` or alternatively `LAUNCH SITE RECOVERY` from `https://<locationCode>srm001.<searchDomain>` URL.
- Click `View Details`, you will be asked to authenticate to the 2nd vCenter Server on the other site (which you may cancel if the protected site is not accessible).
- Click `Recovery Plans`
- Select recovery plan go to `Virtual Machines` tab and at the bottom of the window click `Export` -> `All rows`

As a result of above export actions you must have a list of virtual machines in csv format, containing at least `Virtual Machine` and `Protection Group` variables.

Content of the example CSV exported file.

```csv
"Virtual Machine","Recovery Status","Status Modified By","Protection Group","Priority","Dependencies","Final Power State","vMotion"
"cat-mcm1682-230473902261","Unknown","Unknown","PG22_02-platiniumStorage","3 (Medium)","","Off","Disabled"
"cat-mcm1982-230465858756","Unknown","Unknown","PG22_02-platiniumStorage","3 (Medium)","","On","Disabled"
"dev-mcm1681-230471341693","Unknown","Unknown","PG22_02-platiniumStorage","3 (Medium)","","On","Disabled"
```

## Execute update playbook

1. Copy the exported in previous chapter CSV file into your home directory to *ans001* server.
2. Change directory to */opt/dhc/manage* and execute an update playbook

```bash
   updateVraVmDrTags.yml -e tenant=<tenant name>
```

   >Note: `<tenant name>` For vRA Cloud it's the tenant organization name i.e. 'nx301'. For vRA On-Prem it's the IDM hostname (unless multitenancy is enabled, then it might differ) dedicated for a given tenant, i.e. 'GRE92IDM002'.

   You will be asked for VCS credentials and filename of the uploaded csv file. Please note that playbook requests the change and does not wait for a result, which in case of updating bunch of VMs may take even 15 minutes or longer.
