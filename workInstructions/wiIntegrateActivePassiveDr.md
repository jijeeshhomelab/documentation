# Table of Contents

- [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
- [Related Documents](#related-documents)
- [Infrastructure Requirements](#infrastructure-requirements)
- [Assumptions](#assumptions)
- [Network Requirements](#network-requirements)
- [VCS password manager (HashiCorp Vault)](#vcs-password-manager-hashicorp-vault)
- [Vra cloud tenant organization](#vra-cloud-tenant-organization)
- [Principal storage type](#principal-storage-type)
- [vRA integration type](#vra-integration-type)
- [Integration Steps](#integration-steps)
  - [Step 0 DR variables creation](#step-0-dr-variables-creation)
  - [Step 1 NSX-T distributed firewall configuration](#step-1-nsx-t-distributed-firewall-configuration)
  - [Step 2 SRM service account creation](#step-2-srm-service-account-creation)
  - [Step 3 RBAC on vCenter implementation](#step-3-rbac-on-vcenter-implementation)
  - [Step 4 Workload Domain preparation](#step-4-workload-domain-preparation)
  - [Step 5 SRM server deployment](#step-5-srm-server-deployment)
  - [Step 6 SRM Certificate (VCS CA)](#step-6-srm-certificate-vcs-ca)
  - [Step 7 VSR server deployment \[Manual\]](#step-7-vsr-server-deployment-manual)
  - [Step 8 Register VSR into vCenter \[Manual\]](#step-8-register-vsr-into-vcenter-manual)
  - [Step 9 vRO access validation](#step-9-vro-access-validation)
    - [vRO access validation for vRA Cloud](#vro-access-validation-for-vra-cloud)
    - [vRO access validation for vRA On-prem](#vro-access-validation-for-vra-on-prem)
  - [Step 10 Storing remote SRM svc account in local password manager](#step-10-storing-remote-srm-svc-account-in-local-password-manager)
  - [Step 11 SRM configuration \[manual\] - DNS resolution](#step-11-srm-configuration-manual---dns-resolution)
    - [SRM appliance registration in vCenter](#srm-appliance-registration-in-vcenter)
    - [Network connectivity, DNS resolution](#network-connectivity-dns-resolution)
    - [Site pairing](#site-pairing)
    - [SRM Permissions](#srm-permissions)
    - [Array-based replication - SRA Storage Replication Adapters installation](#array-based-replication---sra-storage-replication-adapters-installation)
    - [Array-based replication - array pairing](#array-based-replication---array-pairing)
    - [Site recovery configuration](#site-recovery-configuration)
  - [Step 12 VRO configuration \[manual\]](#step-12-vro-configuration-manual)
    - [Plugins installation](#plugins-installation)
    - [Disaster Recovery vRO package import](#disaster-recovery-vro-package-import)
    - [SRM plugin configuration](#srm-plugin-configuration)
    - [vRO - DR confguration file](#vro---dr-confguration-file)
    - [VSR configuration](#vsr-configuration)
    - [vRO update proxy](#vro-update-proxy)
  - [Step 13 Validate vRA and vRO integration \[manual\]](#step-13-validate-vra-and-vro-integration-manual)
  - [Token creation](#token-creation)
    - [vRA Cloud token](#vra-cloud-token)
    - [vRA On-prem token](#vra-on-prem-token)
  - [Step 14 vRO subscription](#step-14-vro-subscription)
  - [Step 15 Blueprint update \[manual\]](#step-15-blueprint-update-manual)
  - [Step 16 vRA Service Broker form update](#step-16-vra-service-broker-form-update)
  - [Step 17 Catalog item form validation](#step-17-catalog-item-form-validation)
  - [Step 18 Configure DR monitoring](#step-18-configure-dr-monitoring)
- [Integration Steps - Infoblox configuration for DR](#integration-steps---infoblox-configuration-for-dr)
  - [Step 1 DNS entries](#step-1-dns-entries)
  - [Step 2 Infoblox Grid Master Candidate - export IPAM and DNS configuration](#step-2-infoblox-grid-master-candidate---export-ipam-and-dns-configuration)
  - [Step 3 Infoblox Grid Master Candidate - write down network configuration](#step-3-infoblox-grid-master-candidate---write-down-network-configuration)
  - [Step 4 Infoblox Grid configuration - Grid Master](#step-4-infoblox-grid-configuration---grid-master)
  - [Step 5 Infoblox Grid configuration - Grid Master Candidate](#step-5-infoblox-grid-configuration---grid-master-candidate)
  - [Step 6 Vault](#step-6-vault)
  - [Step 7 Infoblox Grid Master - import IPAM and DNS configuration](#step-7-infoblox-grid-master---import-ipam-and-dns-configuration)
  - [Step 8 vRA proxy update](#step-8-vra-proxy-update)
  - [Step 9 vRA Cloud CAS configuration](#step-9-vra-cloud-cas-configuration)
- [Integration Steps - additional tenant in multi-tenant organization](#integration-steps---additional-tenant-in-multi-tenant-organization)
  - [Step1 New tenant build](#step1-new-tenant-build)
  - [Step 2 vRO access validation](#step-2-vro-access-validation)
  - [Step 3 SRM configuration](#step-3-srm-configuration)
  - [Step 4 VRO configuration](#step-4-vro-configuration)
  - [Step 5 vRO proxy update](#step-5-vro-proxy-update)
  - [Step 6 Validate vRA and vRO integration](#step-6-validate-vra-and-vro-integration)
  - [Step 7 vRO subscription](#step-7-vro-subscription)
  - [Step 8 Blueprint update](#step-8-blueprint-update)
  - [Step 9 Catalog item form validation](#step-9-catalog-item-form-validation)

# Changelog

| Date       | Issue       | Author             | TOS     | Description                                                                               |
|------------|-------------|--------------------|---------|-------------------------------------------------------------------------------------------|
| 06/05/2020 |             | Brian Gerrard      |         | Initial draft version                                                                     |
| 02/10/2020 |             | Robert Kaminski    |         | Changed integration steps layout, playbooks names corrections                             |
| 09/10/2020 |             | Robert Kaminski    |         | Blueprint section adjustment                                                              |
| 13/10/2020 |             | Robert Kaminski    |         | SRM deployment and CA cert section adopted with VCF 4.0                                   |
| 17/10/2020 |             | Robert Kaminski    |         | VSR deployment and configuration section adopted with VCF 4.0                             |
| 20/10/2020 |             | Robert Kaminski    |         | SRM and VRO configuration sections adopted with VCF 4.0                                   |
| 27/11/2020 |             | Robert Kaminski    |         | Added steps: Configure DR monitoring and Nsxt Ruleset                                     |
| 27/11/2020 | DPC-23601   | Robert Kaminski    | VCS 1.2 | TOS 1.2 Ready                                                                             |
| 10/12/2020 | DPC-1020    | Robert Kaminski    | VCS 1.2 | TOS 1.2 post CO review adjustments                                                        |
| 14/04/2021 | DHC-1659    | Tomasz Korniluk    |         | DHC-1659 adjusted chapter Step 7 for VSR hostname and Step 8 vRO                          |
| 27/04/2021 | DHC-1661    | Tomasz Korniluk    |         | Adjusted chapters steps 11-14 to adapt abx vRO                                            |
| 29/04/2021 | DHC-1885    | Radoslaw Dabrowski |         | Adjustment input variables                                                                |
| 27/05/2021 | DHC-1495    | Tomasz Korniluk    |         | Added new chapter to apply vRO patch fix                                                  |
| 07/09/2021 | DHC-2693    | Tomasz Korniluk    |         | Initial updates to cover DR A/P under multitenant org                                     |
| 15/10/2021 | DHC-2700    | Robert Kaminski    |         | Added validation guidelines, added actions related to bidirectional A/P                   |
| 01/12/2021 | DHC-3522    | Lukasz Tomaszewski |         | Integration with IPAM for A/P Bidirectional DR                                            |
| 10.12.2021 | DHC-2799    | Robert Kaminski    |         | Review, doc formatting and linting                                                        |
| 11.05.2022 | DHC-4505    | Robert Kaminski    |         | Doc improvements based on Deployment team inputs                                          |
| 04.11.2022 | CESDHC-4305 | Robert Kaminski    |         | Doc improvement for VMFS on FC as a principal storage for workload domain                 |
| 17.11.2022 | CESDHC-4306 | Robert Kaminski    |         | Added Storage Array Adapters and Array Pairing                                            |
| 24.11.2022 | CESDHC-4614 | Robert Kaminski    |         | Adjusted vRO configuration for FC on SAN as a principal storage for workload domain part2 |
| 02.12.2022 | CESDHC-5073 | Robert Kaminski    |         | Doc improvement for VMFS on FC as a principal storage for workload domain                 |
| 08.12.2022 | CESDHC-4310 | Lukasz Tomaszewski |         | Adjusted proxy exclude list for infoblox                                                  |
| 11.04.2023 | VCS-9318    | Robert Kaminski    |         | Adjustments for vRA OnPrem integration                                                    |
| 24.04.2023 | VCS-9431    | Robert Kaminski    |         | vRA integration type added, adjusted DR variables creation step                           |
| 24.10.2023 | VCF-10602   | Robert Kaminski    |         | Review and adjustments                                                                    |

## Introduction

### Purpose

Configure components that are used to integrate Active Passive Disaster Recovery within VCS.

### Audience

- VCS Operations

### Scope

It is advised that you read the Active Passive Disaster Recovery LLD for more detailed information on DR design decisions.  
That work instruction is intended to cover below tasks and activities:

1. Step by Step instructions to Integrate Active Passive DR (includes multi-tenant organizations)
2. How to execute Automated Ansible Playbooks.
3. How to execute any manual tasks required.

# Related Documents

| Document                                                                 |
|--------------------------------------------------------------------------|
| [LLD Disaster Recovery](../design/lldDisasterRecovery.md)                |
| [LLD Software Defined Networks](../design/lldSoftwareDefinedNetworks.md) |
| [WI A/P Failover](wiFailoverActivePassiveDr.md)                          |
| [WI Disaster Recovery SDN](wiDisasterRecoverySdn.md)                     |

# Infrastructure Requirements

1. Two VCS stand-alone sites fully deployed, hardened.
2. Sites network connectivity meets SDN LLD requirements.
3. Multi or single tenant organization setup implemented under vRA cloud.
4. Active and Passive sites have been identified (when one direction DR type is chosen).
5. Knowledge of DR network variables - eg vSphere Replication Networks.
6. Platform Administrative rights in the VCS mgmt Active Directory on both sites
7. Access to Ansible VMs on Active and Passive sites
8. Permissions to vRA Cloud - Cloud Assembly
9. Abx proxy appliance with embedded vRO in the VCS mgmt cluster
10. Luns created, zoned to hosts and replication enabled when VMFS on FC used as an principal storage for compute workload.

# Assumptions

There is an assumption that the engineers following this process have a good understanding of VMware products and can navigate vCenter and vRA Cloud/Cloud Assembly.

# Network Requirements

Connection between the Active and Passive sites is described in the Software Defined Network LLD.

# VCS password manager (HashiCorp Vault)

VCS uses HashiCorp Vault Password Manager. VCS platform administrators have privileges to login and **read** passwords from Vault.

>Warning: Credentials for all components are stored during deploy, refreshed in the hardening stage phase automatically. It is also valid for playbooks in the manage (production phase). Adjusting credentials manually will result later in failures in automation.

- connect to the password manager via web on port 8200, change method to **LDAP**. Use login in UPN format `<dasId>@<customerCode>dhc<dhcInstance>.next`
![Integration Steps](images/wiIntegrateActivePassiveDr/hashi1.png)

- navigate through secrets path to find credentials applicable credentials
![Integration Steps](images/wiIntegrateActivePassiveDr/hashi2.png)

# Vra cloud tenant organization

VCS integrates with vRA Cloud or vRA OnPrem.

DR A/P implementation relays on tenant configuration, hence at least first tenant organization must be created. Proper A/P DR site integration is applied by joining both sites to the single tenant organization and distinguish them via projects.

To understand the details refer to the following documents:

- [Tenant Builder on vRA Cloud](../workInstructions/wiTenantBuilder.md)
- [Tenant Builder on vRA on-prem](../workInstructions/wiTenantBuilderVraOnPrem.md)
- [Tenant Builder on vRA on-prem multitenant](../workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md)

In case A/P resources shall be shared across multiple tenant organizations, chapter [Integration steps for additional tenant](#integration-steps---additional-tenant-in-multi-tenant-organization) of this WI describes how to achieve it.

# Principal storage type

As per VCF requirements the management domain requires vSAN for the principal storage.

VCS allows to use vSAN or VMFS on FC as a principal storage for the compute workload domain. Active-Passive DR integration differs depending on the principal storage type of the compute workload domain.

VCS uses *principalStorageTypeCmp* variable stored in *group_vars/all* input file to distinguish `vsan` versus `vmfs` storage type of the compute workload.

# vRA integration type

VCS integrates with vRA Cloud or vRA on-prem.

Make sure you have proper type of vRA integration set in *manage/group_vars/all* file:

- `vraType: "saas"` when VCS is integrated with vRA Cloud
- `vraType: "on-prem` when VCS is integrated with vRA on-prem

>It's very important to validate `vraType` variable as the DR integration playbooks will rely on it!

# Integration Steps

Under the conditions all the prerequisites are met, especially first tenant organization created in vRA, you may proceed with disaster recovery integration steps.

Active-Passive DR is integrated on top of two stand-alone VCS sites.

*Active* site is often named *protected* site.

*Passive* site is named *recovery* site.

Workloads running on the active site are protected and might failover to the recovery site, the passive one.

Two types of active-passive setup is possible:

1. A/P one-direction - passive site resources are are not used for active workload, standby for the failover.
2. A/P bi-direction - Customer workload runs on both sites, each site has active area and passive area on the other site.

> Most installation steps are to be executed on both sites. Some on the active site only in case of the one-direction A/P approach. Check the guidelines in the respective chapters.
>
> Note: Steps 0-8 are to be performed on both sites by default. You may perform all of them on one site first and repeat all steps on other site, or do it simultaneously. It's up to your preference.
>
> WARNING! `The screenshots are illustrative and CAN'T be used as source for input data!`
>
> SRM service must be configured in step 12 in line with Customer specific requirements. `Contact integration architect in order to define upfront Customer needs and align it within VCS design boundaries`.

## Step 0 DR variables creation

`Action executed on both sites.`

The first step is to create the variables required for the automated ansible roles.
Logon to the ansible server and browse to `/opt/dhc/manage`.
Execute playbook: createDr0Vars.yml

```shell
dhc/manage$ ansible-playbook createDr0Vars.yml
```

>Note: you will be prompted for some inputs depending on variables:
>
>- Remote DR location name which is Customer specific variable represented by 3 letters and 2 digits - `always visible`
>- vSphere Replication network - first 3 octets only - `visible when: principalStorageTypeCmp == 'vsan'`
>- vSphere Replication default gateway - last octet only - `visible when: principalStorageTypeCmp == 'vsan'`
>- vSphere Replication subnet mask - `visible when: principalStorageTypeCmp == 'vsan'`
>- vSphere Replication VLAN - `visible when: principalStorageTypeCmp == 'vsan'`
>- vSphere Replication network MTU size - `visible when: principalStorageTypeCmp == 'vsan'`
>- vSphere Replication Port Group Name - `visible when: principalStorageTypeCmp == 'vsan'`
>- Remote DR Replication Network (x.x.x.x/x format) - `visible when: principalStorageTypeCmp == 'vsan'`
>- Remote DR Management Network (x.x.x.x/x format) - `visible when: principalStorageTypeCmp == 'vsan'`
>- Remote DR local region network (x.x.x.x/x format) - `always visible`
>- Remote DR cross region network (x.x.x.x/x format) - `visible when: vraType == 'on-prem'`
>- IP address of remote site first ABX (last octet only) - `visible when: vraType == 'saas'`
>- IP address of remote site second ABX (last octet only) - `visible when: vraType == 'saas'`
>
>Warning: If you are doing DR integration with vRA on-prem and you were requested to provide ABX related inputs, make sure you have `vraType: "on-prem"` variable set properly in *managed/group_vars/all* file. Last two ABX related inputs are considered for vRA Cloud only, not valid for vRA onPrem. Similary for `principalStorageTypeCmp: "vmfs"` variable, all vSphere Replication related informations won't be needed as the data sync is performed by array mechanism.

Remote DR location name is the recovery site name for respective site.

>To validate:
>
> - check content of *manage/group_vars/drVars.yml* file.

## Step 1 NSX-T distributed firewall configuration

`Action executed on both sites.`

Next, execute the following playbook to open necessary ruleset between active and passive sites

```shell
dhc/manage$ ansible-playbook createDr1NsxtRuleset.yml
```

> To validate:
>
> - login to *nsx001* GUI
> - go to `Security -> Distributed Firewall`, check the `DR` policy group has been created.

![nsxDrRulesValidation](images/wiIntegrateActivePassiveDr/nsxDrRulesValidation.png)

## Step 2 SRM service account creation

`Action executed on both sites.`

Next, execute the following playbook to create the prerequisites AD configuration for the DR infrastructure:

```shell
dhc/manage$ ansible-playbook createDr2Ad.yml
```

Password for SRM service account is being auto generated to stick VCS Active Directory password policies and next stored in VCS Password Manager (Hashi Corp Vault)

> To validate:
>
> - login to HashiCorp Vault *hsv001* GUI
> - check the entry `secrets><customerCode>/<locationCode>/activedirectory/svc-<locationCode>-srm01` exists.

## Step 3 RBAC on vCenter implementation

`Action executed on both sites.`

Next, execute the following playbook to grants SRM administrators group privileges to Customer Workload vCenter.

```shell
dhc/manage$ ansible-playbook createDr3Rbac.yml
```

## Step 4 Workload Domain preparation

`SKIP this step when VMFS on FC type of principal storage is chosen for compute workload domain`

`Action executed on both sites.`

Next,

- execute the following playbook to create replication portgroup and replication vmkernels on all compute hosts.

```shell
dhc/manage$ ansible-playbook createDr4Wl.yml
```

## Step 5 SRM server deployment

`Action executed on both sites.`

Next, execute the following playbook to deploy the Site Recovery Manager appliance on Mgmt Cluster

```shell
dhc/manage$ ansible-playbook createDr5SrmServer.yml
```

## Step 6 SRM Certificate (VCS CA)

`Action executed on both sites.`

Playbook `createDr6SrmCert.yml` prompts for path to Certificate Signing Request file (CSR). Prepare for it in advance.

- [Manually] Generate CSR upfront by logging into SRM VAMI and clicking on *`Certificates -> GENERATE CSR`* tab, save CSR and store it on ansible server. You will be asked to provide some more information like: organization, organization unit, country code, SRM server FQDN and IP address.

- [Manually] ADD generated via playbook CA certificate on SRM VAMI.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM5.png)

`Action executed on both sites.`

```shell
dhc/manage$ ansible-playbook createDr6SrmCert.yml
```

- Under SRM VAMI click *Certificates* -> *CHANGE*, browse generated CA certificate and install it (use *csr.pem.crt* for Certificate file and *csr.pem-ca.crt* for CA chain)

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM5a.png)
  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM5b.png)

- Refresh the appliance page and ensure that installed CA certificate appears under appliance
  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM5c.png)

## Step 7 VSR server deployment [Manual]

`SKIP this step when VMFS on FC type of principal storage is chosen for compute workload domain`

`Action executed on both sites.`

Next,

- Login to compute vCenter server (preferably 2nd vCenter for customer workload `https://<locationCode>vcs002.<customerCode>.dhc<dhcInstance>.next`) with your VCS mgmt AD account or SSO.
- deploy vSphere Replication appliance from OVF, extract it from */opt/binaries/VMWare-vSphere_Replication-8.4.0-18415587.iso* or download it from MyVMware site.
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr1.PNG)

- name the VM, use exact name convention for current build `<locationCode>vsr001`, place the the VM in the Management VMs folder
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr2.PNG)

- add VM to compute `<locationCode>-c01-user-mgmt01` resource pool
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr3.PNG)
- review details

- accept license agreement

- keep 4 vCPU configuration

- select VSAN storage, use default vSAN storage policy which is thin by default.
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr4.PNG)

- provide management network `<locationCode>-c01-pg-mgmt01` and static-manual IP configuration
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr5.PNG)

>**Note:** Additional replication network is assigned to appliance and configured later by playbook.

- customize template section requires a number of parameters, use IPs instead of names. Validate exact network parameters with *group_vars/all* definition on ansible server.

  Enable SSHD - `enable ssh daemon`

  Password - `use strong one, remember it as next playbook prompts for credentials, as the password must be stored in the VCS password manager`

  Hostname (FQDN) - `<locationCode>vsr001.<customerCode>dhc<dhcInstance>.next`

  DHCP IP Version - `Ipv4`

  Default Gateway - `<networkMgmt.cidr>.<networkMgmt.gw>`

  Domain name - `<customerCode>dhc<dhcInstance>.next`

  Domain search path - `<customerCode>dhc<dhcInstance>.next`

  Domain Name Servers - `<mgmtDns.adc001.cidr>.<mgmtDns.adc001.octet>,<mgmtDns.adc002.cidr>.<mgmtDns.adc002.octet>`

  NTP Servers - `<mgmtDns.adc001.cidr>.<mgmtDns.adc001.octet>,<mgmtDns.adc002.cidr>.<mgmtDns.adc002.octet>`

  Management Network IP - `<mgmtDns.vsr001.cidr>.<mgmtDns.vsr001.octet>`

  Management Network Netmask - `<networkMgmt.netmask>`

  File integrity flag - `optional, leave disabled.`

  Host Network Mode - `static`

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr6.PNG)

- accept binding to visible provider
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr7.PNG)

- finish
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr8.PNG)

- power the *vsr001* VM on on the vCenter
- permit root login to *vsr001* appliance over SSH (see [VMware KB article](https://kb.vmware.com/s/article/2112307))
- next, run the playbook that configures replication network and stores root credentials in the VCS password manager

```shell
dhc/manage$ ansible-playbook createDr7ReplicationServer.yml
```

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr9.PNG)

>Validation tip: Login to VCS password manager using your VCS mgmt domain AD credentials and validate the password entered is stored properly. Path is *`<customerCode>/<locationCode>/servers/<locationCode>vsr001/root`*

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr10.PNG)
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr11.PNG)

- VSR SSL certificate creation is made automatically , make sure to copy generated certificate into local TSS and import under VSR VAMI.
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsrCert0.png)

- Login to VSR VAMI UI (appliance) `https://<vsr001 FQDN>:5480` to install new SSL certificate

- Under "Certificates" click "Change" button to import new certificate.
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSRCert1b.png)

- Ensure to copy new SSL certificate password from vault location `<customerCode>/<locationCode>/servers/<locationCode>vsr001/<locationCode>vsr001.sslcert`
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSRCert1a.png)

>**Note:** Make sure to refresh entire appliance webpage to see new certificate under Appliance Certificate table.

- Ensure that certificate installation completed with success as following example
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSRCert1c.png)

## Step 8 Register VSR into vCenter [Manual]

`SKIP this step when VMFS on FC type of principal storage is chosen for compute workload domain`.

`Action executed on both sites.`

Next, vSphere Replication server must be registered

- login to vSphere Server appliance `https://<vsr001_mgmt_IP>:5480` with *admin* credentials from VCS password manager.

- go to *Summary* tab, click on button "Configure Appliance" to open appliance registration wizard.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSR1a.png)

>**Note:** in vSphere 7 PSC is embedded in the vCenter. Both, management and compute vCenters are in linked mode, hence SSO admin `administrator@vsphere.local` credentials are stored under 1st vCenter only in HashiVault

- provide FQDN of compute PSC controller and default port
- provide password for `administrator@vsphere.local` password for the site (see note above) and click *Next*
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSR1b.png)

- accept VCS CA certificate from psc controller , click "Connect"
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSR1c.png)

- select the compute vCenter for which you want to configure with vSphere replication
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSR1d.png)

- provide site name as name of compute vCenter
- provide local host as FQDN of vSphere replication appliance
- provide correct storage traffic IP
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSR1e.png)

- verify provided inputs and proceed with vSphere replication appliance configuration , click "Finish"
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSR1f.png)

>**Note:** in case below error occurs make sure to change storage traffic ip address to correct one , click "Change" to update ip address.
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSR1g.png)

- verify under compute vCenter-> Site Recovery that vSphere replication appliance has been registered
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSR1h.png)

>`If it hasn't been done yet, repeat steps 0-8 on the remote site.`

## Step 9 vRO access validation

`Action executed on both sites, unless you setup one-direction A/P DR, then on active site only.`

---

### vRO access validation for vRA Cloud

Execute the following steps to validate access to vRO using my VMware account (applicable on active site only) for VCS integrated with vRA Cloud.

>**Note:** vRO automatically redirects to vRA cloud (SaaS) login page and requires to provide a My VMware account to authenticate, make sure to setup VCS proxy when required.
>
>**Note:** Make sure to provide full URL to ABX using FQDN, vRO web UI only works with FQDN.

- Open a browser on the Active site and Log in to vRO under ABX appliance: `https://<locationCode>abx001.<activeDirectory.domainName>:443/vco` and click on "Start the Orchestrator Client".
![Integration Steps](images/wiIntegrateActivePassiveDr/integratonvRoAbx1.PNG)

- vRO automatically redirects to vRA cloud login console, provide My VMware account credentials and sign in.
![Integration Steps](images/wiIntegrateActivePassiveDr/integratonvRoAbx2.PNG)

- When login is successful vRO dashboard webpage should be available
![Integration Steps](images/wiIntegrateActivePassiveDr/integratonvRoAbx3.PNG)

>Note: Access to vRO is mandatory to proceed with configuration of SRM plugin, in case lack of access make sure to validate roles and permission at vRA cloud level.

---

### vRO access validation for vRA On-prem

To reach the vRO on the VCS integrated with vRA on-prem, use the link `https://<locationCode>vra001.<customerCode>dhc<dhcInstance>.next/vco`.

>**Note:** vRO automatically redirects to IDM login page and requires to provide a VCS management domain account to authenticate.

## Step 10 Storing remote SRM svc account in local password manager

`Action executed on both sites.`

Password managers are not synchronized across sites by design. Use VCS management AD accounts to login to HashiVault password manager on each site.

Execute the below playbook to create a Vault entry of the **REMOTE SRM** service account in the **LOCAL** password repository.

```shell
dhc/manage$ ansible-playbook createDr10createVaultEntry.yml
```

In the example below you store the remote site (`gre22`) srm account `<svc-gre22-srm01>` credentials in the local site (`gre12`)

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSrm6.PNG)

Please note *srm* service account purpose is not to register SRM in the vCenter (lack of sufficient privileges) but to reach password manager and authenticate in vRO workflows.

## Step 11 SRM configuration [manual] - DNS resolution

`Action executed on both sites, unless you setup one-direction A/P DR, then on active site only.`

---

### SRM appliance registration in vCenter

Next, configure SRM appliance to register in the vCenter

>Note: in vSphere 7 and higher PSC is embedded in the vCenter. Both, management and compute vCenters are in linked mode, hence SSO admin `administrator@vsphere.local` credentials are stored under 1st vCenter only.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVsr13.PNG)

- provide user and password for SSO, use FQDN of the 2nd vCenter.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM1.png)

- Select the vCenter you want to work with (in this case select our Customer Workload vCenter). Click Next and accept any certificate requests.

- logon to SRM appliance management page, eg. `https://<locationCode>srm001.<customerCode>dhc<dhcInstance>.next:5480/`. Enter the credentials for the user *admin* (stored in local HashiCorp Vault password manager). On the summary tab click *CONFIGURE APPLIANCE*
  
   ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM7.png)

- provide user and password for SSO, use FQDN of the 2nd vCenter.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM1.png)

- Select the vCenter you want to work with (in this case we want our Customer Workload vCenter). Click Next and accept any certificate requests.
   ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM2a.png)
   ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM2.png)

- Enter a site name, for example the local site or vCenter. Enter any administrator email address. This is not important but is mandatory in the wizard. Enter the SRM server for the local host. For the extension ID enter a custom extension ID and name it "workload". This ID is important and should be used on both sites. Enter details on the org and click next
   ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM3.png)
   >NOTE: Be sure to use the same EXTENSION ID, or else pairing will NOT WORK

- Complete the configuration and wait for it to complete registration.

---

### Network connectivity, DNS resolution

Before site pairing can be done, make sure to address in advance below requirements.

- `Network connectivity`. Flows opened for vCenters (tcp443), SRM and VRS (tcp8043) components between sites.

- `DNS resolution`. Protected/active sites must resolve names and IPs of "SRM" related components of the Recovery/Passive site. It's recommended to establish a Stub-Zone or create Primary DNS zone to satisfy vCenters, SRM and VSR hostA forward and reverse lookup resolution. See green rectangles below. DNS resolution must work in both direction, hence you will have to create similar on the second site.

   ![Integration Steps](images/wiIntegrateActivePassiveDr/dns1.png)

>`for bi-directional A/P, on the other site, repeat SRM appliance registration to vCenter and handle DNS resolution`

---

### Site pairing

Next, execute below steps to **PAIR** the sites together. From this moment you have to decide which site will be **Protected/Active** and which the **Recovery/Passive** one.

- In the **Active** site vCenter, again browse to menu and site recovery. Under the workload vCenter, click `Open site recovery`. Login with SSO `administrator@vsphere.local`. Click `NEW SITE PAIR`.
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRM4.png)

- Select the Pair Type, peer vCenter Server located in a different SSO domain
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSrm8.PNG)

- Select the Workload vCenter and enter the FQDN of the PSC from the **Protected/PASSIVE** site. Keep the port as default 443 and enter the corresponding credentials for this PSC. Click Next and accept and certificate requests.
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSrm11.PNG)

- Under vCenter Server and Services, depending on principal storage type:
  - select both vSphere replication and SRM for VSAN
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSrm9.PNG)
  - select SRM only for VMFS on FC.

- finish pairing.

---

### SRM Permissions

- Go to SRM site pair tab and under permission section add dedicated SRM resource group called `rsce-<locationCode>-srm-l-admin` with administrator role. You may check `Propagate to children` option while adding permissions.
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSRMgroup.png)
  
>`Repeat permission step on the passive site to add resource group called` `rsce-<passivelocationCode>-srm-l-admin`.

---

### Array-based replication - SRA Storage Replication Adapters installation

`Skip for VSAN compute workload domain principal storage type.`

When `VMFS` is chosen as a principal storage type for workload domain, the data replication between sites arrays is being arranged by storage mechanism out of Site Recovery Manager. Site Recovery Manager requires Storage Replication Adapters (SRA) installed. Refer to VMware document on how to [Add Storage Replication Adapters to the Site Recovery Manager Appliance](https://docs.vmware.com/en/Site-Recovery-Manager/8.5/com.vmware.srm.admin.doc/GUID-A16E7F4D-8C63-4E4A-8300-3244F41C53DD.html).

The main steps are (below example 1 on DELL EMC Unity 380F Arrays):

- Go to [VMware downloads](https://my.vmware.com/web/vmware/downloads), select `VMware Site Recovery Manager` > `Download Product`, select version and then select `Drivers & Tools` > `Storage Replication Adapters` > `Go to Downloads`
  ![SRA download](images/wiIntegrateActivePassiveDr/sra-download.png)
Search for valid SRM version and valid SRA type... i.e. VCS Engineering development is based on DELL EMC Unity Array.
  ![SRA unity](images/wiIntegrateActivePassiveDr/sra-download-unity.png)

- Login to SRM Appliance Management interface as `admin` user, click `Storage Replication Adapters` > `New Adapter`
  ![SRA new adapter](images/wiIntegrateActivePassiveDr/sra-newadapter.png)

- click `Upload`
  ![SRA upload](images/wiIntegrateActivePassiveDr/sra-upload.png)
  
  Repeat SRA Upload on the other site

- `RESCAN ADAPTERS` and validate the status of both sites
   ![SRA rescan adapters](images/wiIntegrateActivePassiveDr/sra-rescanadapters.png)

---

SRA example 2 - NetApp storage based

The step are mainly the same as for DELL EMC, SRA must be downloaded and added to SRM. So you may rescan adapters.

![SRA rescan adapters netapp](images/wiIntegrateActivePassiveDr/sra-netapp1.png)

The difference is that the vendor requires installation of additional appliance NetApp ONTAP tools. This can be installed with `<locationCode>ont001` name and an IP taken from vSphere Replication appliance (`<mgmt_cidr>.48`), which is not used for array based replication. ONTAP is integrated with vCenter.

![SRA netapp ontap tools](images/wiIntegrateActivePassiveDr/sra-netapp2.png)

Additionaly new appliance requires connectity and adoption of firewall rules.

Please refer to vendor recommendations and guidences. You might also ask Aviva integration and operation team to share their experience in using NetApp.

---

### Array-based replication - array pairing

`Skip for VSAN compute workload domain principal storage type.`

Having SRA Storage Replication Adapters installed successfully, the next step is to pair the arrays on the Site Recovery Manager level.

As a prerequisite, Array Operational support must provide credentials for Array Manager with sufficient privileges at the level of Storage Administrator (follow VMware recommendation for respective Storage Unit).

Start site array pairing (below example on DELLEMC Unity 380F Arrays):

- Login to Site Recovery Manager `<locationCode>srm001.<domain_name>`, view details of `Site Pair`. Make sure account is member of `rsce-<locationCode>-srm-l-admin` domain group or use `administrator@vsphere.local` credentials.
   ![SRA site pair details](images/wiIntegrateActivePassiveDr/sra-sitepairdetails.png)

- Go to `Configure`->`Array Based Replication`->`Array Pairs`, click `Add`

- Choose SRA
  ![SRA](images/wiIntegrateActivePassiveDr/sra-arraypair1.png)

- Provide local array manager inputs
  ![SRA local array manager](images/wiIntegrateActivePassiveDr/sra-arraypair2.png)

- Provide remote array manager inputs

- Select the arrays pairs to enable
  ![SRA enable pairs](images/wiIntegrateActivePassiveDr/sra-arraypair4.png)

- Finish
  ![SRA finish](images/wiIntegrateActivePassiveDr/sra-arraypair5.png)

- Choose `Array Pairs` and click `DISCOVER DEVICES` to see storage devices and replication directions
  ![SRA devices](images/wiIntegrateActivePassiveDr/sra-arraypair6.png)

---

### Site recovery configuration

>**Contact integration architect in order to define SRM configuration in line with Customer specific requirements and within VCS design boundaries**. Refer to [Disaster Recovery LLD](../design/lldDisasterRecovery.md)
specificaly to chapter `Integration guidelines and limitation`. Spend the time to have it done right, make sure to provide SRM settings in the blueprint creation step.

For the SRM configuration, consult Customer requirements and VCS design boundaries with Integration Architect, take into account the following:

- **Mappings**. Mappings allow you to specify how Site Recovery Manager maps virtual machine resources on the protected site to resources on the recovery site. Focus on networks, folders and resource mappings.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationSrm10.PNG)

- **Protection Group**. A protection group is a collection of virtual machines that Site Recovery Manager protects together. VCS is VSAN based, therefore is required to protect a specific individual virtual machines over vSphere Replication, regardless of the datastores. Create as many protection groups you need to satisfy Customer requirements, especially in the area of possible planned periodical failover for the specific application and its components. VCS assumes every defined protection group is assigned to at least one recovery plan.  
  >Due to limitation in vRO SRM workflows, Day1 new VM creation will fail when assigned protection group would not be a member of at least one recovery plan. It is strongly  recommended to create *AllProtectedVMs* recovery plan that would include all existing protection groups as a member to avoid the problem. **Note:** All Protection Group names need to start with the **locationCode** associated with a given protected site (i.e. gre32). It is critical, as the dynamic Protection Group lookup action used in the Service Broker form filters available Protection Groups based on that location code.

- **Recovery Plan**. Make sure to include one or more protection groups in a recovery plan. A recovery plan specifies how Site Recovery Manager recovers the virtual machines in the protection groups that it contains. Make sure to define a least one that would include all existing protection groups.

- **Array-Based replication array pairs**. With VMFS principal storage type, the replication between arrays inside array management tool must be configured first to use Array-Based Replicaiton with Site Recovery Manager.

## Step 12 VRO configuration [manual]

`Action executed on all ABXes on both sites, unless you setup one-direction A/P DR, then on active site only.`

>Note: vRO location differs depending on the vRA type.
>
> - **vRA Cloud** - vRO is built-in as docker into ABX appliance. Use ABX link `https://<locationCode>abx001.<customerCode>dhc<dhcInstance>.next/vco-controlcenter/config/login.html` to login to vRO Control Center page. Enter the credentials for the user *root* (stored in local HashiCorp Vault password manager).
>
>![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROPlugin0.png)
>
> - **vRA On-prem** - vRO is embedded as a docker in the vRA. Use vRA link `https://<locationCode>vra001.<customerCode>dhc<dhcInstance>.next/vco-controlcenter/config/login.html` to login to vRO Control Center page. Enter the credentials for the user *root* (stored in local HashiCorp Vault password manager).

---

### Plugins installation

Import plugins required for Active-Passive DR - perform described steps for each of the plugin listed below.

- Log in to vRO Control Center page, use ABX or vRA link depending on the integration type.

- When the session has been authenticated with vRO Control Center using root account, the full management options are listed. Click on the top banner to go to the homepage, then select Manage Plug-ins.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROPlugin1.png)

- Browse to the location of the plugins. These can be found in the ansible server in the */opt/binaries* folder. Use WinSCP to download them locally to the terminal server and locate them, click browse.
  
The plugins are called:

`vsrPlugin<version>.vmoapp` - (vSphere Replication Plugin is required only when **VSAN** is the principal storage type of the compute workload domain, for VMFS on FC vSphere Replication appliance is not deployed hence vsr plugin is not needed).

`srmPlugin<version>.vmoapp`

- Click Upload

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integratonvRoPlugin2.PNG)

- Accept the EULA and click Install

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integratonvRoPlugin3.PNG)

- Orchestrator service will be automatically restarted after plugin installation
  
  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROPlugin4c.png)

- make sure imported plugins are available and enabled. Plugins version must match the exact SRM and VSR appliance version installed (remember the printscreens are ilustrative only).

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROPlugin4a.png)
  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROPlugin4b.png)

---

### Disaster Recovery vRO package import

**If vRO has already been integrated with GitHub - which is default for vRA on-prem integration - Disaster Recovery package import is irrelevant and you may jump to start [SRM plugin configuration](#srm-plugin-configuration) step**.

>To validate git integration:
>
> - Login to vRO and go to `Administration -> Git Repositories`

If such integration is not in place, configure vRO and start importing DR workflows.

- execute the following ansible playbook to import dr package called `net.atos.dhc.disasterRecovery.package`

```shell
dhc/manage$ ansible-playbook createDr11VroConfigure.yml
```

>Note: Playbook will import package that can be found in the ansible server in the */opt/binaries* folder

- Open a browser on the Active site and Log in to vRO under ABX appliance: `https://<locationCode>abx001.<activeDirectory.domainName>:443/vco` and click on "Start the Orchestrator Client"

>Note: Make sure to logon using my vmware account

![Integration Steps](images/wiIntegrateActivePassiveDr/integratonvRoAbx1.PNG)

- Browse `Administration->Inventory`. Check the endpoint integration, especially for vCenter, vSphereReplication and SRM plugins. In case there are any errors search the `Library->Workflows` for the corresponding workflows to fix integration.
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvRoInventory1.png)

- Browse to the `Assets->Packages`. On right site DR package `net.atos.dhc.disasterRecovery.package` should be visible.

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvRoPackage2.png)

---

### SRM plugin configuration

> **Known Bug**: It has been noticed the Register vCenter Server Site workflow fails when using the plug-in with vRealize Orchestrator 8.9.1 and 8.10. Refer to [VMware KB](https://docs.vmware.com/en/vSphere-Replication/8.6/rn/vmware-vrealize-orchestrator-plugin-for-vsphere-replication-86-release-notes/index.html).
>
> `Configure Local sites` workflow fails with message "Server certificate chain is not trusted and thumbprint doesn't match (Workflow:Configure Local Sites / Validate (item9)#1)".
> ![known error1](images/wiIntegrateActivePassiveDr/srm-error1.png)
> To solve, establish SSH connection to each ABX appliance and run the below `vracli` full command:
>
>```shell
> vracli cluster exec -- bash -c 'base64 -d <<< "a3ViZWN0bCAtbiBwcmVsdWRlIGV4ZWMgLXQgJChrdWJlY3RsIGdldCBwb2QgLW4gcHJlbHVkZSAtbCBhcHA9dmNvLWFwcCAtbyBqc29ucGF0aD0iey5pdGVtc1swXS5tZXRhZGF0YS5uYW1lfSIgLS1maWVsZC1zZWxlY3RvciBzcGVjLm5vZGVOYW1lPSQoY3VycmVudF9ub2RlKSkgLWMgdmNvLXNlcnZlci1hcHAgLS0gYmFzaCAtYyAiYmFzZTY0IC1kIDw8PCAnUzFOZlVFRlVTRDBpTDNWemNpOXNhV0l2ZG1OdkwyRndjQzF6WlhKMlpYSXZZMjl1Wmk5elpXTjFjbWwwZVM5cWMzTmxZMkZqWlhKMGN5SUtURTlIWDBaSlRFVTlKRXhQUTBGTVNFOVRWRjlNVDBkZlJrbE1SVjlFU1ZKRlExUlBVbGt2ZG5KdlgyeHZZMkZzWDJ0bGVYTjBiM0psWDNONWJtTXViRzluQ2dwR1NWQlRYMDFQUkVWZlJVNUJRa3hGUkQwaVpXNWhZbXhsWkNJS1JrbFFVMTlOVDBSRlgxTlVVa2xEVkQwaWMzUnlhV04wSWdwcFppQmJXeUFpSkVaSlVGTmZUVTlFUlNJZ1BYNGdYaWdrUmtsUVUxOU5UMFJGWDBWT1FVSk1SVVI4SkVaSlVGTmZUVTlFUlY5VFZGSkpRMVFwSkNCZFhRcDBhR1Z1Q2lBZ0lDQkxVMTlVV1ZCRlBTSkNRMFpMVXlJS1pXeHpaUW9nSUNBZ1MxTmZWRmxRUlQwaVNrdFRJZ3BtYVFvS0NteHZaMTl0WlhOellXZGxLQ2tnZXdvZ0lHVmphRzhnSWxza0tHUmhkR1VnTFMxMWRHTWdJaXNsUmxRbFZDNGxNMDVhSWlsZElDUXhJaUErUGlBa1RFOUhYMFpKVEVVS2ZRb0tablZ1WTNScGIyNGdhVzV6ZEdGc2JGOWpaWEowS0NrZ2V3b2dJQ0FnYkc5allXd2dZV3hwWVhNOUpERUtJQ0FnSUd4dlkyRnNJSEJsYlY5emRISnBibWM5SkRJS0lDQWdJR2xtSUZzZ0xYb2dJaVJoYkdsaGN5SWdYVHNnZEdobGJnb2dJQ0FnSUNBZ0lHVmphRzhnSWtObGNuUnBabWxqWVhSbElHRnNhV0Z6SUdseklHVnRjSFI1TGlCRGIzVnNaQ0J1YjNRZ2MzbHVZMmh5YjI1cGVtVWdkR2hsSUdObGNuUnBabWxqWVhSbExpSUtJQ0FnSUNBZ0lDQnNiMmRmYldWemMyRm5aU0FpUTJWeWRHbG1hV05oZEdVZ1lXeHBZWE1nYVhNZ1pXMXdkSGt1SUVOdmRXeGtJRzV2ZENCemVXNWphSEp2Ym1sNlpTQjBhR1VnWTJWeWRHbG1hV05oZEdVdUlnb2dJQ0FnSUNBZ0lISmxkSFZ5YmlBeENpQWdJQ0JtYVFvZ0lDQWdhV1lnV3lBdGVpQWlKSEJsYlY5emRISnBibWNpSUYwN0lIUm9aVzRLSUNBZ0lDQWdJQ0JsWTJodklDSkRaWEowYVdacFkyRjBaU0J3WlcwZ2FYTWdaVzF3ZEhrdUlFTnZkV3hrSUc1dmRDQnplVzVqYUhKdmJtbDZaU0JqWlhKMGFXWnBZMkYwWlRvZ0pHRnNhV0Z6SWk0S0lDQWdJQ0FnSUNCc2IyZGZiV1Z6YzJGblpTQWlRMlZ5ZEdsbWFXTmhkR1VnY0dWdElHbHpJR1Z0Y0hSNUxpQkRiM1ZzWkNCdWIzUWdjM2x1WTJoeWIyNXBlbVVnWTJWeWRHbG1hV05oZEdVNklDUmhiR2xoY3k0aUNpQWdJQ0FnSUNBZ2NtVjBkWEp1SURJS0lDQWdJR1pwQ2dvZ0lDQWdaV05vYnlBaVUzbHVZMmh5YjI1cGVtbHVaeUJqWlhKMGFXWnBZMkYwWlNBa1lXeHBZWE1nZEc4Z2JHOWpZV3dnYTJWNWMzUnZjbVVpQ2lBZ0lDQnNiMmRmYldWemMyRm5aU0FpVTNsdVkyaHliMjVwZW1sdVp5QmpaWEowYVdacFkyRjBaU0IzYVhSb0lHRnNhV0Z6T2lBa1lXeHBZWE1nZEc4Z2JHOWpZV3dnYTJWNWMzUnZjbVV1WEc0Z1EyVnlkR2xtYVdOaGRHVTZJRnh1SUNSd1pXMWZjM1J5YVc1bklnb0tJQ0FnSUhSbGMzUWdMV1FnTDNWemNpOXNhV0l2ZG1OdkxXTnNhUzltYVhCekx5QjhmQ0J5Y0cwZ0xXa2dMUzF1YjJSbGNITWdMM1pqYnkxalptY3RZMnhwTG5Kd2JRb2dJQ0FnUWtOZlJrbFFVMTlLUVZKVFBTUW9abWx1WkNBdmRYTnlMMnhwWWk5MlkyOHRZMnhwTDJacGNITXZJQzF1WVcxbElDY3FabWx3Y3lvbklDMTBlWEJsSUdaOGVHRnlaM01nZkhSeUlDY2dKeUFuT2ljcENpQWdJQ0JDUTE5UFVGUlRQU0l0Y0hKdmRtbGtaWEp3WVhSb0lDUjdRa05mUmtsUVUxOUtRVkpUZlNBdGNISnZkbWxrWlhJZ2IzSm5MbUp2ZFc1amVXTmhjM1JzWlM1cVkyRnFZMlV1Y0hKdmRtbGtaWEl1UW05MWJtTjVRMkZ6ZEd4bFJtbHdjMUJ5YjNacFpHVnlJZ29LSUNBZ0lHTmxjblJmWm1sc1pUMGtLRzFyZEdWdGNDQXZkRzF3TDJObGNuUXVNUzVZV0ZndWNHVnRLUW9nSUNBZ1pXTm9ieUFpSkhCbGJWOXpkSEpwYm1jaUlENGdKR05sY25SZlptbHNaUW9nSUNBZ2JHOWpZV3dnY21WemRXeDBQU1FvYTJWNWRHOXZiQ0FrUWtOZlQxQlVVeUF0YTJWNWMzUnZjbVVnSkV0VFgxQkJWRWdnTFhOMGIzSmxkSGx3WlNBa1MxTmZWRmxRUlNBdGMzUnZjbVZ3WVhOeklDUkxVMTlRUVZOVFYwOVNSQ0F0YVcxd2IzSjBZMlZ5ZENBdGJtOXdjbTl0Y0hRZ0xXRnNhV0Z6SUNJa1lXeHBZWE1pSUMxbWFXeGxJQ0lrWTJWeWRGOW1hV3hsSWlrS0lDQWdJR3h2WjE5dFpYTnpZV2RsSUNJa2NtVnpkV3gwSWdvS0lDQWdJSEp0SUMxbUlDUmpaWEowWDJacGJHVUtmUW9LWldOb2J5QWlRMkZzWTNWc1lYUnBibWNnZGxKUElFTmxjblJwWm1sallYUmxjeTRpQ2t0RldWTlVUMUpGWDB4SlUxUTlKQ2d2ZFhOeUwyeHBZaTkyWTI4dlkyWm5MV05zYVM5aWFXNHZkbkp2TFhSb2FXNHRZMlpuTG5Ob0lHdGxlWE4wYjNKbElHeHBjM1FwQ214dloxOXRaWE56WVdkbElDSkxaWGx6ZEc5eVpTQmpiMjUwWlc1ME9pQWtTMFZaVTFSUFVrVmZURWxUVkNJS0NpTWdRMjkxYm5RZ2RHaGxJR05sY25ScFptbGpZWFJsY3dwalpYSjBYMk52ZFc1MFBTUW9aV05vYnlBaUpFdEZXVk5VVDFKRlgweEpVMVFpSUh3Z1ozSmxjQ0FpUVd4cFlYTTZJaUI4SUhkaklDMXNLUXBzYjJkZmJXVnpjMkZuWlNBaVJtOTFibVFnSkdObGNuUmZZMjkxYm5RZ1kyVnlkR2xtYVdOaGRHVnpMaUlLQ2lNZ1NYUmxjbUYwWlNCdmRtVnlJR05sY25ScFptbGpZWFJsY3dwbWIzSWdhVzVrWlhnZ2FXNGdKQ2h6WlhFZ01TQWtZMlZ5ZEY5amIzVnVkQ2s3SUdSdkNpQWdZM1Z5Y21WdWRGOWpaWEowUFNRb1pXTm9ieUFpSkV0RldWTlVUMUpGWDB4SlUxUWlJSHdnWVhkcklDSXZRV3hwWVhNNkwzdHBLeXQ5YVQwOUpHbHVaR1Y0SWlrS0lDQmpkWEp5Wlc1MFgyRnNhV0Z6UFNRb1pXTm9ieUFpSkdOMWNuSmxiblJmWTJWeWRDSWdmQ0JoZDJzZ0p5OUJiR2xoY3pvdklIdHdjbWx1ZENBa01uMG5LUW9nSUdOMWNuSmxiblJmY0dWdFBTUW9aV05vYnlBaUpHTjFjbkpsYm5SZlkyVnlkQ0lnZkNCaGQyc2dKeTlDUlVkSlRpQkRSVkpVU1VaSlEwRlVSUzhzTDBWT1JDQkRSVkpVU1VaSlEwRlVSUzhnZTNCeWFXNTBJQ1F3ZlNjcENpQWdhVzV6ZEdGc2JGOWpaWEowSUNJa1kzVnljbVZ1ZEY5aGJHbGhjeUlnSWlSamRYSnlaVzUwWDNCbGJTSUtaRzl1WlE9PScgfCBiYXNoIC0i" | bash -'
> ```
>
> Repeat on all ABX'es.

- Browse to *Library->Workflows->Library->SRM->Configuration*, run workflow `Configure Local sites`

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationVroWF0.PNG)

- Under "Set local site properties" tab enter the FQDN of the local site 2nd vCenter server FQDN, the port of *443* and the path of */lookupservice/sdk*

>Note: Make sure to set checkbox option to ignore certificate warning.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROWF1.png)

- Click tab "Set the connection properties" enter active site vCenter SSO credentials

- Click Run
  
>Note: Make sure to provide SSO credentials of active site vCenter from below vault location
> `<customerCode>/<locationCode>/servers/<locationCode>vcs001/administrator@vsphere.local`

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROWF2.png)

- Confirm that workflow completes successfully

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROWF3.png)

> **Known Bug**: It has been noticed `Configure Remote Site` vRO workflow is unable to reach Remote vCenter server.
> ![known bug2](images/wiIntegrateActivePassiveDr/srm-error2.png)
>
> Proven solution is to SSH to each ABX appliance and run the below commands after adjusting the inputs:
>
> ```bash
> openssl s_client -showcerts -connect FQDNofRemoteVCS002:443 -servername FQDNofRemoteVCS002 </dev/null 2>/dev/null|openssl x509 -outform PEM >rcaCert.pem
> openssl x509 -inform PEM -in rcaCert.pem -outform DER -out rcaCert.cer
> kubectl -n prelude get pods
> # copy vco-app-xxxxxx PodName from above command into below kubectl commands
> kubectl cp rcaCert.cer prelude/vca-app-PodName:/etc/vco/app-server/security -c vco-server-app
> kubectl exec -n prelude -it vco-app-PodName -c vco-server-app -- bash
> cd /etc/vco/app-server/security
> env | grep KS_PASSWORD
> # the password will be required in the next command
> keytool -import -alias kherca -file rcaCert.cer -keystore "/etc/vco/app-server/security/jssecacerts"
> # accept the certificate
> ```
>
> Repeat on all ABX'es.

- Browse to the Library->Workflows->Library->SRM->Configuration, run workflow `Configure Remote Site`

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROWF4.png)

- click on input field "Local Site"

>Note: Make sure to set checkbox option to ignore certificate warnings.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROWF4a.png)

- Select an object which is refering to local site vCenter name where SRM is registered (i.e. `<locationCode>vcs002`).

- Click `Run`

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROWF6.png)

- Ensure that workflow execution completes successfully

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROWF7.png)

---

### vRO - DR confguration file

Modify the DR configuration file which is referenced by the workflows.

- Browse to `Assets -> Configuration -> Library -> DR` and click on `drConfigurationFile`, next click `EDIT`

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROdrconfig0.png)

- Proceed to adjust variables as follows:

Refer to `Applicable to` column in the below table. Most of the entries are to be used for VSAN only and can be left empty when integrating with VMFS on FC as a principal storage for compute workload.

| Variable name              | Applicable to    | Example variable value                                | Description                                                                                                                                                                                |
|----------------------------|------------------|-------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `srmSite`                  | VSAN, VMFS on FC | gre12vcs002                                           | Select local site SRM <BR> ![DR Config](images/wiIntegrateActivePassiveDr/integrationvROdrconfig1.png)                                                                                     |
| `site`                     | VSAN             | gre12vcs002.nx1dhc01.next                             | Select local vSphere replicaton site name <BR> ![DR Config](images/wiIntegrateActivePassiveDr/integrationvROdrconfig2.png)                                                                 |
| `RemoteSRMSite`            | VSAN, VMFS on FC | SRM remote site                                       | Select remote site SRM <BR> ![DR Config](images/wiIntegrateActivePassiveDr/integrationvROdrconfig3.png)                                                                                    |
| `remoteSiteCode`           | VSAN, VMFS on FC | gre22                                                 | Paste remote site code                                                                                                                                                                     |
| `remoteSite`               | VSAN, VMFS on FC | gre22vcs002.nx2dhc01.next                             | Select remote vSphere Replication Site <BR> ![DR Config](images/wiIntegrateActivePassiveDr/integrationvROdrconfig4.png)                                                                    |
| `remoteLsUrl`              | VSAN, VMFS on FC | gre22vcs002.nx2dhc01.next/lookupservice/sdk           | Paste URL path to remote site vCenter lookup service SDK                                                                                                                                   |
| `remoteDomain`             | VSAN             | nx2dhc01.next                                         | Paste remote site domain name                                                                                                                                                              |
| `remoteDatastoreName`      | VSAN             | gre22-c01-vsan01                                      | Paste remote site compute datastore name (`<locationCode>`-`<workloadDomainComputeNumber>`-vsan01)                                                                                         |
| `vaultPort`                | VSAN             | 8200                                                  | Provide port used on vault server in Local and Remote sites (default 8200)                                                                                                                 |
| `vaultPassword`            | VSAN             | read and paste from vault                             | Paste password of service account svc-`<locationCode>`-ans03@`<customerCode>`dhc`<dhcInstance>`.next read from local site vault server                                                     |
| `vaultServer`              | VSAN             | 172.22.24.33                                          | Paste IP of vault server in Local site                                                                                                                                                     |
| `vaultUser`                | VSAN             | `svc-gre12-ans03@nx1dhc01.next`                       | Paste service account name (svc-`<LocalSitelocationCode>`-ans03@`<customerCode>`dhc<dhcInstance>.next) to logon into local site vault server                                               |
| `vaultSecretPathRemoteSrm` | VSAN             | secret/data/nx1/gre12/activedirectory/svc-gre22-srm01 | Paste path to REMOTE SRM credentials which are stored on LOCAL site vault e.g. secret/data/`<customerCode>`/`<localSitelocationCode>`/activedirectory/svc-`<RemoteSiteLocationCode>`-srm01 |

- Ensure to review `drConfigurationFile`, below example shows correctly populated variables values.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROdrconfig5.png)

---

### VSR configuration

`SKIP for principal storage type VMFS on FC for compute workload domain.`

Register credentials of the remote vCenter Server to allow communication between local and remote vSphere Replication appliances - VSRs.
  
- Browse to the `Library->Workflows->Library->vSphere Replication->Remote Site Management`, run workflow `Register VC Site`

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSRregister01.png)

- Provide answer to select VC remote site and remote site SSO credentials
- Provide VC remote site lookup service address (i.e. gre22vcs002.nx2dhc01.next/lookupservice/sdk )
- Select checkbox to ignore certificates warnings
  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSRregister02.png)
  
  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSRregister03.png)
  
After successful registration browse to the `Library->Workflows->Library->vSphere Replication->Remote Site Management`, run workflow `Log in to VC site`
  
![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSRLoginVc01.png)

- Select remote vSphere site (passive site name i.e. gre22vcs002) and click `Run`

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSRLoginVc02.png)
  
Workflow run should completed with success, in case of error validate provided credentials in `drConfigurationFile` or distributed firewall rule sets.
  
  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvSRLoginVc03.png)

- Repeat on vRO on the other site.
  
---

### vRO update proxy

`Action executed on both sites, unless you setup one-direction A/P DR, then on active site only.`

Next, start to update configuration of docker container proxy used by vRO vSphere replication plugin and Infoblox.
Below steps are required to allow direct connection into remote site vCenter.

- Open putty client from bastion host and log in to *abx001* appliance (vRA Cloud) or directly to *vra002* (vRA on-prem). Use root credentials stored in site vault location:

  - secret/data/`<customerCode>`/`<locationCode>`/servers/`<locationCode>`**abx001**/root  
  or
  - secret/data/`<customerCode>`/`<locationCode>`/servers/`<locationCode>`**vra002**/root

- Execute shell command to retrieve output of the current proxy configuration

  ```shell
  vracli proxy show
  ```

- Extract the content of the following values from previous shell output: `proxy-exclude`, `upstream_proxy_host`, `upstream_proxy_port`

- Adjust proxy-exclude output adding at the end `active directory domain name` from remote/passive site and `VIP of the remote/passive Infoblox` like in below example:

  ```text
  proxy-exclude:".local,.localdomain,localhost,10.,192.168.,172.16.,kubernetes,172.22.30.13,.nx1dhc01.next,gre12abx001.nx1dhc01.next,.nx2dhc01.next,172.28.39.62"
  ```

- Run shell command using new proxy-exclude configuration and current proxy host, port values like in below example:

  ```shell
  vracli proxy set --host http://172.22.30.38:3128 --proxy-exclude ".local,.localdomain,localhost,10.,192.168.,172.16.,kubernetes,172.22.30.13,.nx1dhc01.next,gre12abx001.nx1dhc01.next,.nx2dhc01.next,172.28.39.62"
  ```

- Run shell command to apply new configuration (this will result in restart of proxy service)

  ```shell
  vracli proxy apply
  ```
  
  ![vro-fix-apply](images/wiIntegrateActivePassiveDr/proxyFix1.png)
  
- Run final shell command to redeploy environment to use new proxy settings.

  ```shell
  /opt/scripts/deploy.sh
  ```
  
  ![vro-fix-deploy](images/wiIntegrateActivePassiveDr/proxyFix3.png)
  
- Validate the environment redeployment finished with success.

  ![vro-fix-deploy-success](images/wiIntegrateActivePassiveDr/proxyFix4.png)
  
> To validate:
>
> - Open a browser and check if vRO is responding.
> - SSH to *abx001* or *vra002* depending if you use Cloud or on-prem version of the vRA and run `vracli proxy show` command to validate *proxy-exclude* list against.

Perform step 12 on both sites when configuring bidirectional A/P

## Step 13 Validate vRA and vRO integration [manual]

`Action executed on both sites, unless you setup one-direction A/P DR, then on active site only.`

Next, start to validate integration of vRA Cloud with vRO on active site.

- Log in to vRA cloud using my vmware credentials and browse to Cloud Assembly `Infrastructure` tab

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integratonvRo8.png)

- Browse down to `Connection->Integrations`

- List available vRO integration, use filter to search proper `<locationCode>abx001-vro` when Cloud vRA integration is in place

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROvalidateVra1.png)

  or `embedded-VRO` when vRA On-prem is used

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROvalidateVra4.png)

- Open the integration and click `VALIDATE`, ensure credentials were validated successfully.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROvalidateVra2.png)

- Run `START DATA COLLECTION`, ensure data collection status is OK.

  ![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvROvalidateVra3.png)

> Perform step 13 on both sites when configuring bidirectional A/P

## Token creation

Either vRA Cloud or Vra On-prem requires valid user token in order to connect to it via code.

---

### vRA Cloud token

As a prerequisite user requires a valid user token to establish a connection to vRA Cloud. The token is stored in HashiCorp Vault (`secret/<customerCode>/<locationCode>/vracloud/<customerCode>/authorizationToken-<dasId>`) and is valid for 1 day. To create the Token run a playbook:

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

---

### vRA On-prem token

A token is also required in order to establish connection to vRA On-prem. The token is stored in HashiCorp Vault (`secret/<customerCode>/<locationCode>/vraonprem/<customerCode>/authorizationToken-<dasId>`) and is valid for 1 day. To create the Token run a playbook:

```shell
ansible-playbook createVraOnPremAuthToken.yml
```

## Step 14 vRO subscription

`SKIP for principal storage type VMFS on FC for compute workload domain.`

`Action executed on both sites, unless you setup one-direction A/P DR, then on active site only.`

As a prerequisite [a valid user token must exists](#token-creation) to establish connection with vRA. Note, user token is stored in HashiCorp Vault and by default expires after 1day.

Next, start to create the vRA subscription which will pass the DR related workflows to VCS vRO, which is mandatory for VSAN as principal storage for workload domain. This is automated. Go back to the ansible server and execute the following playbook:

```shell
dhc/manage$ ansible-playbook createDr14VroSubscription.yml
```
  
Playbook prompts for site tenant organization name and project name i.e prd001

>When configuring bidirectional A/P, it will be the same tenant organization name on the other site, but different project i.e prd002.  
> To validate:
>
> - check Subscription status on Cloud Assembly `Extensibility -> Subscriptions`

![Integration Steps](images/wiIntegrateActivePassiveDr/integratonvRo13.png)

## Step 15 Blueprint update [manual]

`Action executed on both sites, unless you setup one-direction A/P DR, then on active site only.`

`SKIP for principal storage type VMFS on FC for compute workload domain.`

Next, start to update the vRA Blueprints to offer Active/Passive functionality in line with SRM configuration performed few steps earlier.

- Log in to Cloud Assembly using my vmware credentials
- Click on the `Design` tab and `Cloud Templates`. Locate the blueprint from proper project which will be used to deploy virtual machine (default blueprint name is like `Deploy virtual machine [prd001]`)

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationBlueprint1.png)

>**Note: For remote/passive site blueprint holds name "Deploy virtual machine [prd002]"**
  
- Adjust the *drRpo_tag* tag section to be inline with SRM configuration set in step 11. *drProtectionGroup_tag* is populated automatically through the workflow. Five VM startup priority options ( *drPriorityGroup_tag* ) are static due to SRM limitation and must stay not modified. Potentially highest priority can be removed to reserve them for core services and hide them on the Service Broker form.

```json
  drProtectionGroup_tag:
    type: string
  drRpo_tag:
    type: integer
    enum:
      - 5
      - 15
      - 60
      - 180
      - 600
      - 1440
    default: 180
  drPriorityGroup_tag:
    type: integer
    enum:
      - 1
      - 2
      - 3
      - 4
      - 5
    default: 3
    description: 1 highest - 5 lowest
```

- go to blueprint *VERSION*. Observe shaded green sections mentioned in the previous steps.

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationBlueprint3.png)

- add description, release it to be visible in the catalog (mandatory step to successfully go further with next steps).

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationBlueprint4.png)

- go to *VERSION HISTORY* and *UNRELEASE* the previous version. Keep latest version released ONLY.

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationBlueprint5.png)

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationBlueprint6.png)

## Step 16 vRA Service Broker form update

`Action executed on both sites, unless you setup one-direction A/P DR, then on active site only.`

As a prerequisite [a valid user token must exists](#token-creation) to estabish connection with vRA. Note, user token is stored in HashiCorp Vault and by default expires after 1day.

Next, start to create the vRA Service Broker form to include Disaster Recovery options. This relay on the blueprint updated in the previous step.
Execute the following playbook on the ansible server:

```shell
dhc/manage$ ansible-playbook createDr16ServiceBrokerForm.yml
```

Playbook prompts for `project name` i.e prd001 on one site and prd002 for the other site.
  
Playbook prompts for `tenant organization name`. For vRA Cloud it's i.e. 'nx301', which should be the same for both sites in A/P DR. For vRA On-Prem it's the IDM hostname (unless multienancy is enabled, then it might differ) dedicated for a given tenant, i.e. 'GRE92IDM002'.
  
## Step 17 Catalog item form validation

`Action executed on both sites, unless you setup one-direction A/P DR, then on active site only.`

Next, validate *Deploy virtual machine* catalog item on the Service Broker, if the form is fulfilled properly with tags definitions from blueprint.
  
> To validate:
>
> Execute new request *Deploy virtual machine [prd001]* from Service Broker catalog and check the below:
>
>- is Active-Passive cluster defined?
>- *Enable DR protection VM* checkbox exists?
>- *Disaster recovery* tab pop-up's with DR parameters aligned with blueprint? `valid for VSAN only`
>- for array based replicated storage *Disaster recovery* tab will not be visible while checking *Enable DR protection on VM*. Protection Group assignment is automatically handled via storage policies. `valid for VMFS on FC only`

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationServiceBroker1.png)

## Step 18 Configure DR monitoring

`Action executed on both sites, unless you setup one-direction A/P DR, then on active site only.`

VCS uses VROPs to monitor vSphere Replication and Site Recovery Manager servers.

Execute the following playbook on the ansible server:

```shell
dhc/manage$ ansible-playbook createDr18EnableMonitoring.yml
```

>Note: In case playbook fails, firewall might be a possible root cause because of wrong rule set configuration (ensure that proper TCP ports are open between vROPS nodes and VSR and SRM appliances)  
> To validate:
>
> - Make sure to validate the task outcome looking in vROPS UI. Go to `vRealize Operations Manager->Administrations->Solutions->Other Accounts`
> - vSR Adapter instance creation will be automatically skipped for VMFS on FC principal storage on workload domain.

![Integration Steps](images/wiIntegrateActivePassiveDr/integrationvRops1.png)

# Integration Steps - Infoblox configuration for DR

For IPAM solution, VCS uses two independent Infoblox HA pairs on each site. These Infoblox entities do not share a common database, which can make an issue when one site is in disaster state. Recovery site does not know about IP reservations made before disaster, thus it is likely that IP conflicts may occure in later scenarios. To prevent this Infoblox offers Grid technology, which works based on a distributed database.

>**Note:** There is an inconvenience when joining Infoblox Grid - you will lose an existing configuration. It consists of: Data Management (IPAM, DNS), Administration settigs (Authentication Server Groups, Authentication Policies, Groups), even local admin account.
>
>**Note:** Once Grid Master Candidate joined Grid Master it won't be reachable via it's own FQDN (you will be redirected to Master Grid IP). To access Infoblox - Master Grid FQDN should be used.

Location assumptions:

- Site A = Grid Master = Protected site
- Site B = Grid Master Candidate = Recovery site

## Step 1 DNS entries

You shoud have already configured DNS zones on each DNS server:

- if Stub Zone has been configured, you do not need to update DNS records
- if Primary Zone has been configured, you need to add A records for inf001, inf002 and inf003 in a proper zone. This has to be completed on both sites.

## Step 2 Infoblox Grid Master Candidate - export IPAM and DNS configuration

On Grid Master Candidate site navigate to `Data Management -> Toolbar -> CSV Job Manager` (Toolbar is on the right side of the screen). In the new window select `CSV export` and click `plus` icon. Keep `Comma` as a separator, deselect `All Objects` and mark:

- in the first column: `IPv4 DHCP Ranges, IPv4 Fixed Address, IPv4 Network`
- in the third column: `Network View, Ruleset`
- in the fourth column: `A Record, Authorative Zone. Hosts, PTR Record`

Click `Export Data -> Yes`.

CSV file will be automatically saved in Downloads folder.

## Step 3 Infoblox Grid Master Candidate - write down network configuration

On Grid Master Candidate site navigate to `Grid -> Grid Manager -> Members`, select existing Grid and click `Edit` on a Toolbar located on right side.
On Grid Member Proporties Editor click Network on left menu and write down values for:

- Virtual Router ID
- VIP (IPv4) Address, Subnet mask and Gateway
- Node1 HA (IPv4) Address
- Node2 HA (IPv4) Address
- Node1 LAN1 (IPv4) Address
- Node2 LAN1 (IPv4) Address

## Step 4 Infoblox Grid configuration - Grid Master

On Grid Master site navigate to `Grid -> Grid Manager -> Members`, click `Add` on a Toolbar located on right side.

Change Member Type to Virtual NIOS, provide Grid Master Candidate Host Name (FQDN), select Master Candidate checkbox and click Next.

On the next page select `TYPE OF MEMBER -> High Availability Pair` and provide Virtual Router ID gathered in step 3.

Scroll down to REQUIRED PORTS AND ADDRESSES and provide rest values gathered in step 3.

Click Save & Close.

## Step 5 Infoblox Grid configuration - Grid Master Candidate

On Grid Master Candidate site navigate to `Grid -> Grid Manager -> Members`, click `Join Grid` on a Toolbar located on right side.

Provide Virtual IP of Grid Master, Grid Name (Infoblox), Grid Shared Secret (test) and click OK.

It may take ~15 minutes to join Grid Master and establish all services.

Infoblox will be available under Master Grid FQDN.

## Step 6 Vault

Once Grid Master Candidate joined Grid Master it's starting to use Grid Master admin account (as this one becomes the master one), as well other accounts that were configured on Master Grid, like: automation.

Please update Infoblox admin and automation password. Execute below playbook on `<locationCode>ans001` on Site B.

```shell
/dhc/manage$ ansible-playbook updateInfobloxVaultEntry.yml
```

## Step 7 Infoblox Grid Master - import IPAM and DNS configuration

On Grid Master site navigate to `Data Management -> IPAM` and click `CSV Import` on the Toolbar located on right side. Select `Type of import -> Merge`, click `Next`, choose the CSV file exported in [step 2](#step-2-infoblox-grid-master-candidate---export-ipam-and-dns-configuration), click `Next` and `Import`.

## Step 8 vRA proxy update

Make sure the remote Infoblox VIP is added to the ABX proxy exclude list. Refer to [vRO proxy update](#vro-update-proxy) chapter for details.

## Step 9 vRA Cloud CAS configuration

Login into VMware Cloud Services. Change your organization to correct one and open VMware Cloud Assembly. Go to `Infrastructure -> Integrations`. Click on `<locationCode>abx001-ipam` integration.

Provide Password for automation account and change Hostname IP to the IP assigned to Master Grid. Click `Validate`. If succeeded click `Save`.

# Integration Steps - additional tenant in multi-tenant organization

Additional tenant creation steps are similar to the origin integration task described above. To reduce text duplication we present here the main steps/topics with the reference to original chapters.

>Disclaimer:
>
> - Multi-tenancy was not tested on VCS 1.5 greenfield build, the below steps must be considered as guidenance and not a strict workinstruction.
> - Multi-tenancy was not tested on vRA On-prem.

## Step1 New tenant build

  Follow [WI Tenant Builder](wiTenantBuilder.md) workinstuction to create additional tenant.

  The new Tenant creation brings mandatory and optional actions:

- Datastore cluster creation
- Storage policy creation
- Dedicated ABXes (in HA mode), CASes creation
- Dedicated networks creation in NSX-T.
  >Note: It is recommended to create IPAM integration from Tenant ABX with the existing Infoblox appliance.
- Account creation
- Resource pool configuration

## Step 2 vRO access validation
  
  Validate [access to vRO](#step-12-vro-configuration-manual) on the ABX's created for the new tenant.

## Step 3 SRM configuration

  Review [SRM configuration](#site-recovery-configuration) to reflect parameters like: mappings, protection groups, recovery plans etc... related to Site Rocovery Manager configuration for the new tenant.

## Step 4 VRO configuration

  New tenant has it's on ABXes with VRO running as a docker. Follow [VRO configuration](#step-12-vro-configuration-manual) activities to handle:

- Plugins installation
- vRO package import
- Plugins configuration
- DR config file creation

## Step 5 vRO proxy update

  Refer to chapter [vRO update proxy](#vro-update-proxy) on the new tenant ABXes.

## Step 6 Validate vRA and vRO integration

  Refer to chapter [Validate vRA and vRO integration](#step-13-validate-vra-and-vro-integration-manual).

## Step 7 vRO subscription

  Refer to chapter [vRO subscription](#step-14-vro-subscription).

## Step 8 Blueprint update

  Refer to chapter [Blueprint update](#step-15-blueprint-update-manual).

## Step 9 Catalog item form validation

  Refer to chapter [Catalog item form validation](#step-17-catalog-item-form-validation).
