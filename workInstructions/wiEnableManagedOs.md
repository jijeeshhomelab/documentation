# Implementation of Managed OS in DHC - runbook

## Table of Contents

- [Implementation of Managed OS in DHC - runbook](#implementation-of-managed-os-in-dhc---runbook)
  - [Table of Contents](#table-of-contents)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
  - [Scope](#scope)
  - [Related document](#related-document)
  - [Prerequisites](#prerequisites)
- [Procedure](#procedure)
  - [Update Code Repository](#update-code-repository)
    - [Update Ansible](#update-ansible)
    - [Update vRO](#update-vro)
  - [ServiceNow Integration](#servicenow-integration)
    - [Functional Organization](#functional-organization)
    - [Integration Account](#integration-account)
    - [Windows MID server enablement](#windows-mid-server-enablement)
      - [Deploy Windows MID server](#deploy-windows-mid-server)
    - [CMDB Discovery](#cmdb-discovery)
      - [MID Server Credentials](#mid-server-credentials)
      - [vCenter Configuration](#vcenter-configuration)
      - [Discovery Schedule](#discovery-schedule)
      - [Enable Application CI Detection](#enable-application-ci-detection)
      - [Cloud Event Handler](#cloud-event-handler)
      - [Validation](#validation)
  - [TSSA and TSO Integration](#tssa-and-tso-integration)
    - [TSO Account for Automation](#tso-account-for-automation)
      - [Store TSO Account credentials inside Vault\*\*](#store-tso-account-credentials-inside-vault)
    - [Access to TSSA console](#access-to-tssa-console)
      - [AdminGate Account](#admingate-account)
      - [Infra AD Account](#infra-ad-account)
      - [BSA/TSSA Users Group](#bsatssa-users-group)
      - [Accessing Global BSA/TSSA console](#accessing-global-bsatssa-console)
    - [TSSA Gateway](#tssa-gateway)
      - [Prerequisites](#prerequisites-1)
      - [Firewall Request](#firewall-request)
      - [BSA Gateway Server Deployment](#bsa-gateway-server-deployment)
      - [Onboarding TSSA Gateway](#onboarding-tssa-gateway)
      - [TSSA Agent](#tssa-agent)
      - [Patching Smart Groups](#patching-smart-groups)
  - [CMF Integration](#cmf-integration)
    - [CMF firewall Request](#cmf-firewall-request)
    - [Prerequisites](#prerequisites-2)
    - [Deploy CMF Gateway](#deploy-cmf-gateway)
    - [Onboarding to CMF](#onboarding-to-cmf)
  - [DHC Managed OS Automation](#dhc-managed-os-automation)
    - [Prerequisites](#prerequisites-3)
    - [Mandatory AD permission](#mandatory-ad-permission)
    - [Enabling Managed OS feature using Ansible playbooks](#enabling-managed-os-feature-using-ansible-playbooks)
      - [Example managed OS vars file](#example-managed-os-vars-file)
    - [Store managed OS inputs inside Vault](#store-managed-os-inputs-inside-vault)
- [Validate](#validate)
- [Appendixes](#appendixes)
  - [Integration User Intake Form](#integration-user-intake-form)

## Changelog

 | Date       | Version | Issue     | Author              | Description            |
 | ---------- | ------- | --------- | ------------------- | ---------------------- |
 | 22.11.2024 | 1.0     | VCS-14353 | Krzysztof Olszewski | Initial draft creation |
 | 09.04.2025 | 1.1     | VCS-15056 | Marcin Kujawski     | Add managed OS automation chapter |
 | 06.06.2025 | 1.2     | VCS-16185 | Marcin Kujawski     | Adding SNOW chapter |
 | 14.08.2025 | 1.3     | VCS-15768 | Marcin Kujawski     | Updating implementation procedure and SNOW chapter |
 | 16.01.2026 | 1.4     | VCS-17905 | Tomasz Korniluk     | Updating chapters to address TOS bugs |

## Introduction

### Purpose

This instruction covers steps that are required to implement functionality of Managed OS in DHC. Below points describes that are necessary to introduce functionality into DHC platform.

### Audience

- DHC Engineers
- DHC Architects
  
## Scope

The scope of this document covers:

1. Prerequisites
2. DHC configuration
3. Integration with TSSA and TSO
4. Integration with CMF

## Related document

| Document Name                          |
| -------------------------------------- |
| [lldManagedOS](../design/lldManagedOS.md) |

## Prerequisites

The Instruction assumes that the reader has reasonable grasp of DHC infrastructure and VMware components as well as TSSA and CMF solutions.

1. Functional Organization (FO) needs to exist in SNOW before onboarding to CMF and TSSA.
2. WBS need to be organized for both CMF and TSSA onboarding process.
3. DHC environment installed and fully available.

# Procedure

## Update Code Repository

### Update Ansible

In order to prepare DHC environment to Managed OS, github repository has to be properly configured and cloned to retrieve automation code.

- Login into to DHC management `ans001` machine using domain credentials.
- Go to local home folder:

  ```bash
  cd /home/<username>
  ```
  
- Configure global git options which will apply when working with all repositories:

  ```bash
  git config --global user.name 'GitHub name'
  git config --global user.email 'GitHub email address'
  git config --global core.hookspath 'hooks'
  ```

- Configure proxy server for local git:

  ```bash
  git config --global http.proxy 'http://ProxyServerAddress:ProxyServerPort'
  ```

- Configure github credential store and recurse submodules:

  ```bash
  git config --global credential.helper store
  git config --global submodule.recurse true
  ```

- Verify the configured options:

  ```bash
  git config -l
  ```

- Verify that present location is user home folder:

  ```bash
  pwd
  ```

- Clone the repository:

  ```bash
  git clone https://github.com/GLB-CES-PrivateCloud/dhc.git --recurse-submodules
  ```

  >**Note:** You will be prompted for a Username and Password (GitHub access token) during cloning. If user has no valid Github token please create one as this is required to clone the repository.

### Update vRO

- Login into VMware Aria Automation.
- Navigate to `Orchestrator`.
- Click `Git Repositories` and then `Add repository`.
  
  ![gitRepository](images/wiEnableManagedOs/gitRepository.png)
- Provide `Repository Name` and `Repository URL` like: `https://github.com/GLB-CES-PrivateCloud/VRO-Workflows.git`
- Select `Make Rpository Active`.
- Select `Remote Branch` to `DHC-2.X`.

 >**Note:** Validate with VCS release manager and integration architect desired remote branch for vRO update. and make sure during vRO git repository switch to not overwrite any local changes for existing production deployments.

  ![gitRepositoryConfig](images/wiEnableManagedOs/gitRepositoryConfig.png)

- Go to `Git Repository` section on the left side.
- Click `Pull`.

  ![gitRepositoryPull](images/wiEnableManagedOs/gitRepositoryPull.png)

## ServiceNow Integration

### Functional Organization

In order to start the SNOW Integration there is requirement to have already FO (Functional ORganization) created and chosen. In this construct all configuration need to happen and it is strict requirement to have it ready before continuation to next chapters.

### Integration Account

Request integration account with `RunbookCallback Job Family`.

1. Login to SNOW Prod: [https://atosglobal.service-now.com/](https://atosglobal.service-now.com/)
2. Navigate to **Atos Internal Service Catalog** --> **GDTS** --> **Service Request Management (ServiceNow)** --> **Generic Service Request**.
3. Fill in:

- Customer: *provide FO for Customer*
- Service: **12. Application Integration**
- Sub Service: **Integration User - Create or Modify**
- Request Description: describe the request and mention that integration user need to be aded into `RunbookCallback Job Family`.
- Fill the template available in Appendixes under [Integration User Intake Form](#integration-user-intake-form) and attach it to request.

Example request: `RITM015651031`.

### Windows MID server enablement

The MID Server (Management, Instrumentation, and Discovery) is the key component that connects ServiceNow with your infrastructure. A Windows-based MID Server is required to:

- Collect guest OS WMI-based inspections of Windows Servers.
- SSH into Linux systems (with proper credentials).
- Connect to vCenter APIs and perform vCenter Discovery.
- Run discovery probes and sensors on your behalf.
- Collect guest OS applications information

> Note: Linux MID server is not able to perform WMI-based inspections hence Windows MID is used.

#### Deploy Windows MID server

**Step 1. Collect prerequisite inputs before machine deployment**

The below table describes mandatory inputs before Windows machine deployment:

| Input | Mandatory | Value | Description |
| --------- | ------| ------------- |------------ |
| Machine name | Yes | `<VCsSiteCode>`mid003| MID machine name inside vSphere e.g. gre42mid003|
| Machine vCPU size | Yes | 2| Default vCPU size|
| Machine vMemory size | Yes |2GB| Default vMemory size|
| Resource pool name | Yes | `<VcsSiteLocationCode>`-m01-sddc-mgmt | Management vSphere resource pool name |
| NSXT network segment| Yes | lreg-m01-seg01  | lreg-m01-seg01 network segment |
| Network segment ip address| Yes | lreg-m01-seg01 .53 octet | Take from lreg-m01-seg01 ip address (use .53 octet)|
| Machine DNS record| Yes | `<VCsSiteCode>`mid003.`<VCsSiteDomainName>` (e.g. gre42mid003.nx4dhc01.next)| Create for mid003 DNS record A using `<VcsSiteLocationCode>`adc001 (Domain controller machine) |
| Windows Server OS template name| Yes | GlobalImage_w2k22 |Global Image Windows Server Template name from mgmt vCenter |
| Customer FO MID server account credentials| Yes | <Obtain credentials from Snow Support request> |Dedicated ServiceNow MID server account credentials to establish connection (obtain from Atos ServiceNow support  team)|
| MID server instance name|Yes| DIS-`<VCsCustomerName>`-VCS-`<VCsSiteCode>`MID003|MID server instance name visible inside ServiceNow instance|
| ServiceNow instance URL |Yes| By default production `https://atosglobal.service-now.com/`|Dedicated Production ServiceNow instance|
| VCS platform proxy url and port | Yes | http://`<VCsSiteLocationCode>`pxy001.`<VCsSiteDomainName>`:3128 (example `http://gre42pxy001.nx4dhc01.next:3128`|Dedicated VCS platform proxy instance |

**Step 2. Create NSXT firewall rules**

The below table describes mandatory firewalls rules required to allow MID server traffic

> Note: The below rules should be implemented automatically during platform deployment. Make sure to validate if exists below rules (inside NSX001), in case missing implement.

| Item name | Type | Value | Source | Destination | Service | Mandatory | Description |
| --------- | ------| ------------- |------------ |---------- |---------- |---------- |---------- |
| MidServer_APPLYTO | Dynamic security group | `<VcsSiteLocationCode>`mid001,`<VcsSiteLocationCode>`mid002,<VcsSiteLocationCode>mid003|N/A|N/A|N/A|Yes| Defines list of members |
| MidServerToVcs | Firewall rule|MidServerToVcs|`<VcsSiteLocationCode>`seg046|`<VcsSiteLocationCode>`seg013|HTTPS|Yes| Allows traffic from mid server into management and compute vCenter instances |
| MidServerToAd | Firewall rule|MidServerToAd|`<VcsSiteLocationCode>`seg046|`<VcsSiteLocationCode>`seg006|Windows-Global-Catalog|Yes| Allows traffic from mid server into mgmt domain controllers |

**Step 3. Obtain MID server installation file**

To implement it please perform following steps:

1. Login to SNOW Prod: [https://atosglobal.service-now.com/](https://atosglobal.service-now.com/)
2. Navigate to Mid Server > Downloads
3. Select and download the MID Server for the appropriate operating system. For the best performance, install the 64-bit MID Server for your operating system (Windows 64bit)
4. Save the download file to a temporary file on the local drive inside terminal machine (TSS001)

>Note: Optionally desired MID server installation file should exists inside ans001 machine in location `/data/binaries`

**Step 4. Provision MID server windows machine**

To implement it please perform following steps:

1. Logon into Management vCenter instance using VCS AD domain account
2. Navigate to resource pool `<VcsSiteLocationCode>`-m01-sddc-mgmt (e.g. gre42-m01-sddc-mgmt)
3. Right-click on selected resource pool and choose option `New Virtual Machine`
4. Select VM creation option `Deploy from template`
5. Click on tab `VM templates` and navigate into `Templates` folder
6. Under `Template` folder select desired OS template `GlobalImage_w2k22`
7. Provide virtual machine name (e.g. gre42mid003) (use name from pre-requisite inputs)
8. Select desired resource pool as compute resource
9. Make sure to choose correct network segment for first vNIC (use network segment from pre-requisite inputs)
10. Select management workload domain datastore (e.g. gre42-m01-vsan)
11. Select checkbox `Customize this virtual machine's hardware` and click `OK`
12. Click `Finish` button to proceed with virtual machine deployment
13. Validate if virtual machine has been deployed under desired resource pool and make sure to Power On
14. Logon into virtual machine using vSphere console using OS template credentials (stored in key vault path `./templates/GlobalImage_w2k22/`)
15. Setup manually hostname for the virtual machine
16. Setup manually desired ip configuration (use settings from pre-requisite inputs)
17. Add virtual machine into the VCS platform mgmt AD domain (use ans01 service account in case permission issues)
18. Reboot virtual machine and validate if acccessible using AD domain

**Step 5. Install MID server application**

1. Upload from Terminal server (TSS001) MID server installation file into mid003 machine
2. Store MID server installation file inside folder `C:\temp\`
3. Create new folder on drive C: as follows `C:\servicenow\`
4. Inside `C:\servicenow` create subfolder with name DIS-`<VCsCustomerName>`-VCS-`<VCsSiteCode>`MID003 (e.g. DIS-Acme01-VCS-GRE42MID003)
5. Install Windows MID server application using the following ServiceNow official procedure: [MID Server on Windows with guided installation](https://www.servicenow.com/docs/bundle/yokohama-servicenow-platform/page/product/mid-server/concept/mid-server-install-prereqs.html)

>Note: **Make sure to install Windows MID server application using local administrator account and select option `use proxy`**

**Step 6. Request in ServiceNow MID server validation**

1. Create ticket in ServiceNow to validate the MID server.
2. Login to SNOW Prod: [https://atosglobal.service-now.com/](https://atosglobal.service-now.com/)
3. Navigate to **Atos Internal Service Catalog** --> **GDTS** --> **Service Request Management (ServiceNow)** --> **Generic Service Request**.
4. Fill in:

- Customer: *provide FO for Customer*
- Service: **12. Application Integration**
- Sub Service: **MID server - Validate or Update**
- Request Description: describe the request and mention about MID server to be validated
- Ensure the MID Server status is Up and validated and has assigned a capability tags required for MID server and added parameter `mid.ssh.use_snc`

> Note: Requested ticket should be processed by dedicated Atos ServiceNow support team in 1-2 working days.

**Step 7. Validate MID server in ServiceNow**

After *this step* you should have:

- MID Server up and running

![MID Server Configuration](images/wiEnableManagedOs/snowIntegration0.png)

- MID Server validated and assigned capabilities as well as parameter `mid.ssh.use_snc`

![MID Server Configuration](images/wiEnableManagedOs/snowIntegration1.png)

**Step 8. Store MID server application credentials inside Vault**

1. Logon into Hashi Corp Vault (url e.g. https://`<VCsSiteCode>`hsv001.`<VCsSiteDomainName>`:8200) using domain account with role `rsce-dhc-vlt-l-passwordadmins`
2. Create secret path key `<VcsCustomerName>`/`<VcsSiteCode>`/servers/`<VcsSiteCode>`mid003/administrator with value password from `./templates/GlobalImage_w2k22`
3. Under same key path `<VcsCustomerName>`/`<VcsSiteCode>`/servers/`<VcsSiteCode>`mid003/ create another key for MID application discovery ServiceNow account credentials (e.g.xxxxx.midserver@xxxxx)
4. Save change and logout from HashiCorp Vault

### CMDB Discovery
  
> Note: Please be informed that all those activities need to be done by SNOW team via RITM request. Tasks are described for informational purpose only - to know what kind of activities need to be handled in order to complete SNOW integration for Managed OS completely.

Request CMDB discovery and create SNOW request.

1. Login to SNOW Prod: [https://atosglobal.service-now.com/](https://atosglobal.service-now.com/)
2. Navigate to **Atos Internal Service Catalog** --> **GDTS** --> **Service Request Management (ServiceNow)** --> **Generic Service Request**.
3. Fill in:

- Customer: *provide FO for Customer*
- Service: **16. Discovery**
- Sub Service: **Configure-MID server**
- Request Description: describe the request and mention about all required tasks as mentioned below

#### MID Server Credentials

To enable Server CI creation the following credentials are required:

- Windows servers credentials to access OS level (local or domain service account can be used for this)
- Linux servers credentials to access OS level (local or domain service account can be used for this)

Make sure that credentials are corresponding to the configuration done on Ara Automation blueprints. Username and password need to match otherwise CMDB discovery process will not be able to login and fetch OS details.

#### vCenter Configuration

This step defines what targets will be scanned by Discovery and how to identify them. SNOW team should set:

- vCenter integration (for VMware infrastructure)
- IP ranges for scanning (customer VM's network ranges)
- Events that trigger on-demand Discovery (`VmDeployedEvent`, `VmReconfiguredEvent`, etc.)
- Credentials used during scanning

#### Discovery Schedule

Scheduled discovery ensures CMDB stays up to date even without event triggers. Daily schedule for vCenter Discovery should be activated in order to provide up to date state of CMDB.

#### Enable Application CI Detection

To detect higher-level services like SQL Server and IIS, Application Pattern Discovery should be enabled and configuration for the relevant application patterns should be set.

Make sure that SNOW configuration enables Application Pattern for SQL and IIS - as those two are required.

#### Cloud Event Handler

In order to automate Server CI creation as well as setting automatically proper attributes to enable monitoring for CI - Cloud Event Handler is used to achieve this. After each request there is an API call with specific payload sent from Orchestrator server to SNOW. Based on payload Cloud Event Handler is doing one of the following:

- create Server CI and update it with attributes to enable monitoring on this CI
- decommission Server CI (Retire the CI and disabling monitoring automatically)

Cloud Event Handler is named: **Cloud Event Handler - DHC Event Handler**.

To trigger the event handler the custom header is used: `user-agent: dhccmdbhandler`

Cloud Event Handler was created as a part of standard SNOW CMDB implementation.

Example payload to create Server CI (sent from vRO Orchestrator):

```sh
curl curl -sk -X POST -u 'dcshc.dhc@atf-dhc.com:<pass>' --location -x http://172.22.28.38:3128 'https://atosglobalcat.service-now.com/api/now/cloud_event?event_name=DHC&resource_id=Cloud%20Event%20Handler%20-%20DHC%20Event%20Handler' \
--header 'user-agent: dhccmdbhandler' \
--header 'Content-Type: application/json' \
--data '{
   "install_status":"Installed",
   "operational_status":"Live",
   "u_monitoring_tool_name":"ATF-NAGIOS",
   "u_monitoring_object_id":"test-linux01",
   "ip_address":"172.17.27.114",
   "assignment_group":"ZZ.Cloud.DHC-DevSecOps",
   "u_is_monitored":true,
   "fqdn":"test-linux01.nx1dhc01.next",
   "name":"test-linux01",
   "dns_domain":"nx1dhc01.next",
   "sys_class_name":"cmdb_ci_linux_server",
   "u_data_source":"DHC",
   "serial_number":"VMware-42 1f fa 42 ca ff 5c c0-fc 94 ce 06 ca 7f 16 33",
   "u_os_reference":"Red Hat Enterprise Linux 8",
   "os": "",
   "virtual":true
}'
```

Example payload to decommission Server CI (sent from vRO Orchestrator):

```sh
curl curl -sk -X POST -u 'dcshc.dhc@atf-dhc.com:<pass>' --location -x http://172.22.28.38:3128 'https://atosglobalcat.service-now.com/api/now/cloud_event?event_name=DHC&resource_id=Cloud%20Event%20Handler%20-%20DHC%20Event%20Handler' \
--header 'user-agent: dhccmdbhandler' \
--header 'Content-Type: application/json' \
--data '{
   "install_status":"Retired",
   "operational_status":"Decommissioned", 
   "u_is_monitored":false,
   "sys_class_name":"cmdb_ci_linux_server",
   "u_data_source":"DHC",
   "serial_number":"VMware-42 1f fa 42 ca ff 5c c0-fc 94 ce 06 ca 7f 16 33"
}'
```

#### Validation

After *this step* you should have:

- vCenter event collector configured

![MID Server Configuration](images/wiEnableManagedOs/snowIntegration2.png)

- Discovery schedule with proper IP Ranges created

![MID Server Configuration](images/wiEnableManagedOs/snowIntegration3.png)

- Discovery Credentials created

![MID Server Configuration](images/wiEnableManagedOs/snowIntegration4.png)

- Credentiale linked with specific MID Server or set to `All MID servers` (regarding the MID Servers in particular domain)

![MID Server Configuration](images/wiEnableManagedOs/snowIntegration5.png)

- Cloud Event Handler script is available in SNOW

![MID Server Configuration](images/wiEnableManagedOs/snowIntegration7.png)

## TSSA and TSO Integration

### TSO Account for Automation

In order to execute automated task in TSSA, the dedicated account for automation in TSO is necessary. It can be requested by sending email to TSSA team members to create one. Below is the example of such email:

```text
Hello Team
 
Please kindly create an automation account in TSO for Customer "<customerName>".
Account is required to automatic provisioning of new Managed OS feature for DHC private cloud. 

Kind regards
```

Send this email to:

- <dominika.pacek@atos.net>
- <piotr.cyrklaff@atos.net>
- <kamil.gesicki@atos.net>
- <jan.gawinski@atos.net>

#### Store TSO Account credentials inside Vault

1. Logon into Hashi Corp Vault (url e.g. https://`<VCsSiteCode>`hsv001.`<VCsSiteDomainName>`:8200/) using domain account with role `rsce-dhc-vlt-l-passwordadmins`
2. Create secret path key `<VcsCustomerName>`/`<VcsSiteCode>`/managedos
3. Under same key path `<VcsCustomerName>`/`<VcsSiteCode>`/managedos create another key with name `tso` and store TSO account credentials
4. Save change and logout from HashiCorp Vault

### Access to TSSA console

#### AdminGate Account

The SAaCon-AD-Access is required to access AdminGate. This generic role can be assigned by Line Managers, so please ask your Line Manager (defined in DAS) to assign you this role.

#### Infra AD Account

The Intra-AD-Access is required to access TSSA application. This role can be assigned by Line Managers, so please ask your Line Manager (defined in DAS) to assign you this role.

#### BSA/TSSA Users Group

To get access to BSA/TSSA console, belonging to the `"GG-SSE-BSAUsers group"` is necessary. It can be obtained by opening a SNOW ticket according to the following process:

- Open [SNOW](https://atosglobal.service-now.com/sp?id=sso&portal-id=atos) portal.
- Go to [User Management](https://atosglobal.service-now.com/now/nav/ui/classic/params/target/com.glideapp.servicecatalog_category_view.do%3Fv%3D1%26sysparm_parent%3D2ef0fab71b4d69904740dc6fe54bcb26%26sysparm_ck%3D917fc2df870aa5d04cbc646f8bbb350106eecccfa2cc812ab523d1c90e53f375c3298ddd%26sysparm_catalog%3De0d08b13c3330100c8b837659bba8fb4%26sysparm_catalog_view%3Dcatalog_default%26sysparm_cartless%3Dtrue).
- Select `Create/Modify/Remove AD Group`.
- In `Work notes` section provide request like:
  >Description: Please assign AD Group "GG-SSE-BSAUsers" to the following user: \<DASID\>  
  Domain name: ad.intra.ao-srv.com  
  Group Name: GG-SSE-BSAUsers
- Create ticket.
- Go to [BSA-User Management](https://atosglobal.service-now.com/nav_to.do?uri=/com.glideapp.servicecatalog_cat_item_view.do?v%3D1%26sysparm_id%3Df248f7d3dbe3445024d2bb1a68961971%26sysparm_link_parent%3D2974b86fdb3094108471a7c74896199e%26sysparm_catalog%3D9e6d62600f5d9a409850ecd692050eed%26sysparm_catalog_view%3Dcatalog_Atos_Internal_Service_Catalog).
- Select the company to which user should be given the role (DHC customer).
- Select `Option` to `Create`.
- Click `Add`.
- Choose user name.
- Select checkbox roles: `Windows` and `Linux`.
- Attach Line Manager's approval as email and ask Line Manager to Approve ticket in SNOW.
- Create ticket.
- Go to [General Service Request](https://atosglobal.service-now.com/nav_to.do?uri=/com.glideapp.servicecatalog_cat_item_view.do?v%3D1%26sysparm_id%3D9f874dd40f38434058311b1e51050e09%26sysparm_link_parent%3D4b189ca60f60138058311b1e51050ec0%26sysparm_catalog%3De0d08b13c3330100c8b837659bba8fb4%26sysparm_catalog_view%3Dcatalog_default).
- Select `Service` to `BBSA`.
- Select `Sub Service` to `Users Create`.
- Create ticket.
- Fill in [Intake form](https://atos365.sharepoint.com/:x:/r/sites/690001424/ATF/ServerMgt/BBSA/_layouts/15/Doc.aspx?sourcedoc=%7BE7AE20C4-D945-4CD6-9CC9-962A0976D6A4%7D&file=PLF-TSE-0008_BladeLogic%20Server%20Automation%20-%20User%20Account%20IntakeForm.xlsm&action=default&mobileredirect=true).
- Attach `Intake Form` and Line Manager's approval as email to created ticket.

#### Accessing Global BSA/TSSA console

In [TSSA SharePoint](https://atos365.sharepoint.com/sites/690001424/tools/TSSA/SitePages/2.%20FAQ%20and%20Knowledge%20Base.aspx), in section `To access the Global TSSA Console follow these steps`, you can find actual information how to access TSSA administrator's console.

### TSSA Gateway

#### Prerequisites

The below table describes mandatory inputs before provisioning of TSSA Gateway machine:

| Input | Mandatory | Value | Description |
| --------- | ------| ------------- |------------ |
| Machine name | Yes | `<VCsSiteCode>`bsa001| BSA machine name inside vSphere e.g. gre42bsa001|
| Machine vCPU size | Yes | 4| Default vCPU size|
| Machine vMemory size | Yes |8GB| Default vMemory size|
| Resource pool name | Yes | `<VcsSiteLocationCode>`-m01-sddc-mgmt | Management vSphere resource pool name |
| Resource pool name | Yes | `<VcsSiteLocationCode>`-m01-sddc-mgmt | Management vSphere resource pool name |
| NSXT network segment| Yes | lreg-m01-seg01  | lreg-m01-seg01 network segment |
| Network segment ip address| Yes | lreg-m01-seg01 .52 octet |Take from lreg-m01-seg01 ip address (use .52 octet)|
| Machine DNS record| Yes | `<VCsSiteCode>`bsa001.`<VCsSiteDomainName>` (e.g. gre42bsa001.nx4dhc01.next)| Create for bsa001 DNS record A using `<VcsSiteLocationCode>`adc001 (Domain controller machine) |
| BSA Gateway OVA template | Yes |tsssaproxytemp01.ova |Obtain OVF template from [Repository](https://bsaum.nl.ms.myatos.net/vm-images/) download `tsssaproxytemp01.ova` image in the latest available version there. |

**Download from [Repository](https://bsaum.nl.ms.myatos.net/vm-images/) `tsssaproxytemp01.ova` image in the latest available version there.**

**Store download file inside VCS platform Terminal Server (TSS001) in location `C:\temp`**

>Note: Make sure after successfull BSA Gateway deployment to delete downloaded image in location `C:\temp` inside Terminal Server (TSS001)

#### Firewall Request

In order to proper communication between TSSA and DHC environments network traffic need to be opened. It guarantees the communication between the gateway and TSSA core infrastructure. To request the firewall opening, ASN standard change request is mandatory to be created within [SNOW](https://atosglobal.service-now.com/now/nav/ui/classic/params/target/com.glideapp.servicecatalog_category_view.do%3Fv%3D1%26sysparm_parent%3Dffc71c660f60138058311b1e51050eb5%26sysparm_no_checkout%3Dfalse%26sysparm_ck%3D4a9eecdd87061a946cdea6440cbb356364076695ea0153fb09b26b5575a33b7997b1cc28%26sysparm_catalog%3D9e6d62600f5d9a409850ecd692050eed%26sysparm_catalog_view%3Dcatalog_Atos_Internal_Service_Catalog) portal. Please request all necessary ports which need to be opened as described in [lldManagedOS](../design/lldManagedOS.md#221-network-port-and-protocol-requirements) documentation.

> **Note**: Also local (DHC) firewall has to be configured according to TSSA Gateway requirements use BSA gateway ip address from prerequisite chapter

#### BSA Gateway Server Deployment

To provision BSA gateway follow below steps:

1. Logon into Management vCenter instance using VCS AD domain account
2. Navigate to resource pool `<VcsSiteLocationCode>`-m01-sddc-mgmt (e.g. gre42-m01-sddc-mgmt)
3. Right-click on selected resource pool and choose option `Deploy OVF Template` on location
4. Select option `Local file` and point to OVF file placed in directory `C:\temp` on Terminal Server
5. Provide virtual machine name (e.g. gre42bsa001) (use name from prerequisite inputs)
6. Select desired resource pool as compute resource
7. Make sure to choose correct network segment for first vNIC (use network segment from pre-requisite inputs)
8. Select management workload domain datastore (e.g. gre42-m01-vsan)
9. Select checkbox `Customize this virtual machine's hardware` and click `OK`
10. Click `Finish` button to proceed with virtual machine deployment
11. Validate if virtual machine has been deployed under desired resource pool and make sure to Power On

>Note: The rest steps to configure ip configuration and BSA gateway setup is delivered by TSS Support Team. Make sure to grant access into mgmt vCenter for support team.

#### Onboarding TSSA Gateway

- Create ticket in [SNOW](https://atosglobal.service-now.com/now/nav/ui/classic/params/target/com.glideapp.servicecatalog_cat_item_view.do%3Fv%3D1%26sysparm_id%3D9f874dd40f38434058311b1e51050e09%26sysparm_link_parent%3D4b189ca60f60138058311b1e51050ec0%26sysparm_catalog%3D9e6d62600f5d9a409850ecd692050eed%26sysparm_catalog_view%3Dcatalog_Atos_Internal_Service_Catalog).
- Attach `TrueSight Server Automation - On-Boarding Intake Form.xlsm` which you can find [here](https://atos365.sharepoint.com/sites/690001424/ATF/ServerMgt/BBSA/Shared%20Documents/Forms/Shared.aspx?csf=1&web=1&e=LsD2im&CID=fe208dba%2D84ea%2D480c%2Daed9%2D41d2258ab3ad&FolderCTID=0x0120008B1A9CBB33DEAE40830FDFB5D29DB07E&id=%2Fsites%2F690001424%2FATF%2FServerMgt%2FBBSA%2FShared%20Documents%2F02%2E%20Onboarding%20Kit%2F01%2E%20Onboarding%20documents).

#### TSSA Agent

To properly managed servers using BSA/TSSA, an agent has to be installed. It is done automatically during VM deployment. However, initially agents have to be downloaded from [SharePoint](https://atos365.sharepoint.com/sites/690001424/ATF/ServerMgt/BBSA/Shared%20Documents/Forms/Shared.aspx?id=%2Fsites%2F690001424%2FATF%2FServerMgt%2FBBSA%2FShared%20Documents%2FTSSA%20RSCD%20Agents&viewid=9f6b8d3a%2D1ba1%2D4972%2D9940%2Dc2af7c0f80d9) or from [ASN network](https://bsaum.nl.ms.myatos.net/BSAGER/RSCD-recent/). and placed inside VCS platform central repository share (deb001 machine).

**Steps to upload TSSA agent**

1. Download agent from TSSA team sharepoint site [SharePoint](https://atos365.sharepoint.com/sites/690001424/ATF/ServerMgt/BBSA/Shared%20Documents/Forms/Shared.aspx?id=%2Fsites%2F690001424%2FATF%2FServerMgt%2FBBSA%2FShared%20Documents%2FTSSA%20RSCD%20Agents&viewid=9f6b8d3a%2D1ba1%2D4972%2D9940%2Dc2af7c0f80d9)
2. Temporarily store the file locally under Terminal server (TSS001)
3. Obtain next user account credentials from Vault secret path servers/`<VCsSiteCode>`deb001/next
4. Upload the file to repository machine inside location (deb001) http://`<VCsSiteDeb001-IP>`/ManagedOS/TSSA/
5. Validate if share is accessible and uploaded file exists

**(Optional) Agents have to be uploaded into S3 and public access rights need to be assigned to allow proper download for those installation files.**

#### Patching Smart Groups

Creation of patching `Server Smart Groups` is required to allow fully automated server patching process. The number of patching groups can vary and depends on customer requirements and agreements. Please gather the information from Customer to implement patching windows for both Linux and Windows OS flavors. As per now the following OS types are supported.

| Vendor    | Product        | Release Version |
| --------- | -------------- | --------------- |
| Microsoft | Windows Server | 2022            |
| Red Rat   | RHEL           | 9.x             |

To create a patching server groups:

- Login into `TSSA console`.
- Select proper role:  

  1. `<customerCode>`_L3AdminL for Linux server (i.e. `DHC_L3AdminL`)
  2. `<customerCode>`_L3AdminW for Windows server (i.e. `DHC_L3AdminW`)

- Go to `Servers -> <customerCode>`
- Click right mouse button and select `New -> Server Smart Groups`
- Provide smart groups name according to contract agreement. Name need to clearly state which period of time it is for, like `Patching Window 1st Sat` or similar.
  
  ![tssaConsoleSmartGroups](images/wiEnableManagedOs/tssaConsoleSmartGroups.png)

## CMF Integration

Onboarding to CMF procedure you can find in [CMF Team SharePoint](https://atos365.sharepoint.com/sites/690001424/tools/cmf/SitePages/cmf-onboarding.aspx). Please follow `Dedicated CMF Gateway` scenario.

### CMF firewall Request

In order to proper communication between CMF and VCS CMF gatewat network traffic need to be opened. It guarantees the communication between the CMF gateway and CMF core infrastructure. To request the firewall opening, ASN standard change request is mandatory to be created within [SNOW](https://atosglobal.service-now.com/now/nav/ui/classic/params/target/com.glideapp.servicecatalog_category_view.do%3Fv%3D1%26sysparm_parent%3Dffc71c660f60138058311b1e51050eb5%26sysparm_no_checkout%3Dfalse%26sysparm_ck%3D4a9eecdd87061a946cdea6440cbb356364076695ea0153fb09b26b5575a33b7997b1cc28%26sysparm_catalog%3D9e6d62600f5d9a409850ecd692050eed%26sysparm_catalog_view%3Dcatalog_Atos_Internal_Service_Catalog) portal. Please request all necessary ports which aneed to be opened as described in [lldManagedOS](../design/lldManagedOS.md#221-network-port-and-protocol-requirements) documentation.

More information inside CMF sharepoint site [CMF sharepoint site](https://atos365.sharepoint.com/sites/690001424/tools/cmf/SitePages/cmf-onboarding.aspx)

### Prerequisites

The below table describes mandatory inputs before provisioning of CMF gateway machine:

| Input | Mandatory | Value | Description |
| --------- | ------| ------------- |------------ |
| Machine name | Yes | `<VCsSiteCode>`cmf001| CMF machine name inside vSphere e.g. gre42cmf001|
| Machine vCPU size | Yes | 4| Default vCPU size|
| Machine vMemory size | Yes |8GB| Default vMemory size|
| Resource pool name | Yes | `<VcsSiteLocationCode>`-m01-sddc-mgmt | Management vSphere resource pool name |
| NSXT network segment| Yes | lreg-m01-seg01  | lreg-m01-seg01 network segment |
| Network segment ip address| Yes|.54 octet |Take from lreg-m01-seg01 ip address|
| Machine DNS record| Yes | `<VCsSiteCode>`cmf001.`<VCsSiteDomainName>` (e.g. gre42cmf001.nx4dhc01.next)| Create for cmf001 DNS record A using `<VcsSiteLocationCode>`adc001 (Domain controller machine) |
| CMF Gateway OVF template| Yes |GlobalImage_RHEL8.1|Obtain template from compute vCenter Content library (available under `<VcsSiteCode>`-p-cl01)|

### Deploy CMF Gateway

To provision CMF gateway the below steps :

1. Logon into Management vCenter instance using VCS AD domain account
2. Navigate to resource pool `<VcsSiteLocationCode>`-m01-sddc-mgmt (e.g. gre42-m01-sddc-mgmt)
3. Right-click on selected resource pool and choose option `New Virtual Machine`
4. Select VM creation option `Deploy from template`
5. Click on tab `OVF & OVA templates` and select OS template `GlobalImage_RHEL8.1` under Library name `<VcsSiteCode>`-p-cl01
6. Provide virtual machine name (e.g. gre42cmf001) (use name from prerequisite inputs)
7. Select desired resource pool as compute resource
8. Make sure to choose correct network segment for first vNIC (use network segment from pre-requisite inputs)
9. Select management workload domain datastore (e.g. gre42-m01-vsan)
10. Select checkbox `Customize this virtual machine's hardware` and click `OK`
11. Click `Finish` button to proceed with virtual machine deployment
12. Validate if virtual machine has been deployed under desired resource pool and make sure to Power On
13. Logon into virtual machine using vSphere console using OS template credentials (stored in key vault path `./templates/GlobalImage_RHEL8.1/`)
14. Setup manually hostname for the virtual machine
15. Setup manually desired ip configuration (use settings from pre-requisite inputs)
16. Create local disk path /appl/Nagios and set size to 64 GB
17. Reboot virtual machine and validate if acccessible
  
### Onboarding to CMF

- Fill in `Intake Form` which you can find [here](https://atos365.sharepoint.com/:x:/r/sites/690001424/ATF/ServerMgt/CMF/_layouts/15/Doc.aspx?sourcedoc=%7bE347F3EE-09A6-49AA-9BB5-16E07C110183%7d&file=PLF-TSE-0012_ATF%20CMF%20-%20Onboarding%20Intake.xlsm&wdOrigin=TEAMS-ELECTRON.p2p.bim&action=default&mobileredirect=true) and attach it to ticket.
- Create ticket in SNOW according to procedure [Raising a ticket to CMF queue](https://atos365.sharepoint.com/sites/690001424/tools/cmf/SitePages/links-and-contact.aspx).

## DHC Managed OS Automation

### Prerequisites

The below table describes mandatory inputs and files obtained before starting the managed OS automation:

| Name | Mandatory | Value | Description |
| --------- | ------| ------------- |------------ |
| managedOsVars.yml | Yes |-| File needs to be created in user home directory (see sub-chapter)|
| customInfraVars.yml | Yes |-| File needs to copy from /home/next folder into existing user home directory |
| datacenter | Yes |Example: DHC_Les_Clayes| Input requires name of datacenter for BSA integration|
| gridName | Yes |Example: GridA| Name of BSA grid name|
| instance | Yes |Example: eu| SA instance region|
| linRoleName | Yes |Example: DHC_L3AdminL| BSA Linux role name |
| winRoleName | Yes |Example: DHC_L3AdminW| BSA Windows role name|
| serverName | Yes |Example: 161.89.115.217| BSA gateway server ip|
| proxy | Yes |Example: 161.89.115.217:3128| VCS platform local proxy ip and port|
| tsoUsername | Yes |-| TSO user name delivered by TSSA team|
| tsoPassword | Yes |-| TSO user password delivered by TSSA team|
| localAdmin | Yes |mos_adm| VCS Managed OS local admin user name|
| customerDomains | Yes |See example described inside sub chapter| Json payload that contains customer domain name,dns,OU path,user account to join vms into domain |
| repoServerUsername | Yes |-| VCS deb repository user account (obtain from vault secret key `<VcsSiteCode>`/servers/`<VcsSiteCode>`deb001/)|
| repoServerPassword | Yes |-| VCS deb repository user password (obtain from vault secret key `<VcsSiteCode>`/servers/`<VcsSiteCode>`deb001/)|
| repoServerUrl | Yes |-| VCS deb repository ip address/name e.g. https://`<VcsSiteCode>`deb001/ManagedOS|
| monitoringServer | Yes |-| CMF Gateway machine ip|
| instanceUrl | Yes |`https://atosglobalcat.service-now.com` or `https://atosglobalprod.service-now.com` | ServiceNow instance url|
| username| Yes |-| ATF2 integration user account name|
| password| Yes |-| ATF2 integration user account password|
| datasource| Yes |-| Datasource name, usually customer name e.g. Acme01 |
| monitoringToolName| Yes |ATF-NAGIOS| ATF Nagios monitoring tool name|
|supportGroup| Yes |ZZ.Cloud.DHC-DevSecOps| Atos VCS support group (devsecops)|
|eventHandlerEndpoint| Yes |/api/now/cloud_event?event_name=DHC&resource_id=Cloud%20Event%20Handler%20-%20DHC%20Event%20Handler| Static url to ServiceNow event handler |

### Mandatory AD permission

The mandatory permissions to join Managed OS virtual machine into Customer domain

| Account type | Mandatory AD permissions | Password lifecycle | Account role |
| --------- | ------| ------------- |------------ |
| AD service account | Domain admin |6 months| Allows to join virtual machine into Customer domain|

### Enabling Managed OS feature using Ansible playbooks

The following steps are executed by Ansible automation:

- validate Managed OS inputs for TSSA
- create Aria Automation refresh and bearer tokens
- create SOAP Host for TSSA
- create Powershell Host for Customer AD
- create Managed OS config file
- create Managed OS subscriptions (for create and delete activities)
- update project provisioning timeout to 1h

Before running the automation, please create `managedOsVars.yml` file with all Managed OS details. File should be located in user home directory.
>Note: Make sure to validate if exists inside user home directory file: `customInfraVars.yml`

#### Example managed OS vars file

Example file is presented below.

```yaml
bsa:
  customerName: "DHC"
  datacenter: "DHC_Les_Clayes"
  gridName: "GridA"
  instance: "eu2"
  linRoleName: "DHC_L3AdminL" 
  winRoleName: "DHC_L3AdminW"
  serverName: "161.89.115.217"
  serverPort: 38080
  proxy: "172.22.28.38:3128"
  tsoUsername: "DHC_SRV"
  tsoPassword: ""
  localAdmin: "mos_adm"
  customerDomains: '{"nx1dhc01.next":{"dns1":"172.22.16.24","dns2":"172.22.16.25","ou":"OU=Servers,OU=DHC,DC=nx1dhc01,DC=next","username":"a545604","password":"U0REQ2NsZDEwIVNERENjbGQxMCE="},"nx3dhc01.next":{"dns1":"172.22.48.24","dns2":"172.22.48.25","ou":"OU=Servers,OU=DHC,DC=nx3dhc01,DC=next","username":"administrator","password":"Z0R5aVgwdTNoTHRQSnZjRw=="}}'
  supportInformation: '{"default-tenant":{"portalUrl":"https://gre12vra005.nx1dhc01.next","email":"default-tenant-contact@nx1dhc01.next","phone":"+48 123 456 789","contact":"SDM Firstname Lastname"},"tenant1":{"portalUrl":"https://tenant1.gre12vra005.nx1dhc01.next","email":"tenant1-contact@nx1dhc01.next","phone":"+48 123 456 789","contact":"SDM Firstname Lastname"}}'
dhc:
  repoServerUsername: "depot"
  repoServerPassword: ""
  repoServerUrl: "https://10.99.94.159/ManagedOS"
cmf:
  monitoringServer: "172.22.16.210"
snow:
  instanceUrl: "https://atosglobalcat.service-now.com"
  username: "dcshc.dhc@atf-dhc.com" 
  password: ""
  dataSource: "DHC"
  monitoringToolName: "ATF-NAGIOS"
  supportGroup: "ZZ.Cloud.DHC-DevSecOps"
  eventHandlerEndpoint: "/api/now/cloud_event?event_name=DHC&resource_id=Cloud%20Event%20Handler%20-%20DHC%20Event%20Handler"
```

Execute the **enableManagedOs.yml** playbook on ans001 server.

 ```yml
ansible-playbook enableManagedOs.yml
 ```

You will be prompted to provide the following inputs:

- VCS management Active Directory domain username i.e. `a123456@exampledomain.com` and password
- Tenant name
- Managed OS platform (by default TSSA)
- Customer Active Driectory FQDN
- Customer AD service account username and password

Once playbook execution is finished please double check following items:

- SOAP Host and Powershell Host have been successfully created.
- Login to Aria Automation, open Orchestrator, navigate to the Configuration and verify that the Managed OS is present in DHC folder.
- Login to Aria Automation, open Assembler, navigate to Extensibility and validate that `{{ projectName }}-createManagedOS` subscriptino for Managed OS is created.
- Login to Aria Automation, open Assembler, navigate to Infrastructure, click on Projects and once go into particular one, validate that in Provisioning tab Request Timeout is set to 1h.

### Store managed OS inputs inside Vault

Based on chapter DHC Managed OS Automation prerequisites make sure to store below inputs under Vault secret key `<VcsCustomerCode>`/`<VcsSiteCode>`/managedos/.

- repoServerUsername
- repoServerPassword
- tsoUsername
- tsoPassword
- (snow) username
- (snow) password

# Validate

The below table list mandatory managed OS functionalities that requires validation test's.

**Make sure to execute below tests to conclude validation for managed OS integration**

| Functionality test | Mandatory | Scope | Test description |
| --------- | ------| ------------- |------------ |
| Windows Server Deployment with managed OS | Yes | Cloud Template with managed OS parameters| Validate if selected parameters provision Windows Server as managed OS VM|
| Linux Server Deployment with managed OS | Yes | Cloud Template with managed OS parameters| Validate if selected parameters provision Linux Server as managed OS VM|
| Server Deployment with managed OS (AD domain join) | Yes | Cloud Template with AD join parameters| Validate if selected parameters works and VM has been added into the Customer AD|
| Validate VM in BSA console | Yes | BSA console VCS group contains VM | Validate if provisioned VM has been added into the Customer BSA group and visible|

# Appendixes

## Integration User Intake Form

[ATF ServiceNow Integration User Template](ATF_ServiceNow_Integration_Template.docx)
