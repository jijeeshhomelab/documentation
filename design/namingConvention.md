# Naming Convention

- Table of Contents {:toc}

# Naming Convention principles

Below documentation is intended to cover naming standards for VCS management components.

## Variable naming

Please not that two different types of variable are used in naming convention:

- variables: these names are provided inside curly brackets { exampleVar }
- non-variables: these names are provided inside angle brackets < exampleVar >

`Variable` type **has** an Ansible representative instance in VCS code.
`Non-variable` type **has not** an Ansible representative instance in VCS code.

## Active directory

### Domain name

Active directory domain for VCS management must consist customer code `customerCode` followed by VCS instance number `dhcInstance` and suffix `.next` in the end.
> **Example:**
_atoDHC01_ where `ato` represents `customerCode` variable, `DHC` is static entry and `01` is VCS instance number.
> NOTE: static entry part os is a soft requirement and can be redefined. For details, refer to VCS active directory build automation documentation
> NOTE: VCS instance number is a sequence number related to particular Customer VCS instance starting with `01` which indicates first VCS deployment. If Customer has more than one VCS next valid number should be used accordingly.

### Service accounts name

### AD Objects name

## Server names

All VCS management servers must be described as `locationCode` followed by static `serverRole` prefix and `number` where:

- `locationCode` is customer specific variable represented by 3 letters and 2 digits
- `serverRole` represents 3 lower-case letter role prefix, like:  _vcs_ for vcenter _adc_ for Active Directory.
- `number` represents sequential order of particular server in chosen location and is represented by 3 digits starting from _001_ up to _999_

> **Example:**
_mec01vcs001_ where `mec01` represents `locationCode`, `vcs` represents server role and `001` represents server number in that location

Server roles:

| serverRole     | Description                |
|----------------|----------------------------|
| alb | Advanced Loadbalancer |
| adc | Domain Controller |
| ans | Ansible Core |
| avp | Avamar Proxy |
| awx | Ansible AWX |
| cb  | Cloud builder appliance |
| cht | CloudHealth|
| ctl | NSX Controller |
| deb | Ubuntu deb Packages Repository |
| edg | NSX Edge |
| hgw | HTTP Gateway for SNOW |
| hsv | HashiCorp Vault |
| ica | Issuing Certificate Authority |
| idm | Workspace ONE Access |
| inf | Infoblox |
| lcm | Aria Suite Lifecycle Manager |
| nes | Nessus |
| nsx | NSX Manager |
| mid | Mid Server |
| ops | Aria Operations Manager |
| psc | vCenter Platform Services Controller |
| pxy | Squid Proxy |
| rca | Root Certificate Authority |
| rep | Offline Bundle Transfer Utility |
| rpx | Reverse proxy |
| sdm | SDDC manager |
| srm | Site Recovery Manager |
| srs | SMTP Relay server |
| tss | Bastion Host |
| vcs | vCenter Server |
| vli | Aria Operations for Logs |
| vnc | Aria Operations for Networks Collector |
| vni | Aria Operations for Networks |
| vsr | vSphere Replication |
| vwa | vSAN Witness |
| wus | Windows Update Server |

## versionMatrix specific elements

All server roles abbreviations included in versionMatrix can be found in `Server names` subchapter. Below you can find a list of elements from versionMatrix not related to any server role:

| Element | Description                                                            |
|---------|------------------------------------------------------------------------|
| tpl     | Template, i.e: GlobalImage_w2k16                                       |
| cl      | Content Library                                                        |
| ci      | Cloud Init                                                             |
| cb      | VMware Cloud Builder is a virtual appliance that is used to deploy VCF |

## VMware Cloud Foundation elements

**vCloud Foundation Objects - Workload Domain (Compute only)**  
The naming standard is defined as: < customerCode >-< locationCode >-< type >-< workloadDomainNumber >

| Definition           | Description            | Details                             |
|----------------------|------------------------|-------------------------------------|
| customerCode         | Customer short name    | 3 alphanumeric characters (a-z,0-9) |
| locationCode         | Location short name    | 3 alpha characters (a-z) + 2 digits |
| type                 | (c)ompute              | 1 alpha character (c)               |
| workloadDomainNumber | workload domain number | 2 digits                            |

e.g. nx2-gre02-c01

**vCenter Objects - Datacenter**  
The naming standard is defined as: locationCode-type-dc

| Definition   | Description               | Details                             |
|--------------|---------------------------|-------------------------------------|
| locationCode |                           | 3 alpha characters (a-z) + 2 digits |
| type         | (m)anagement or (c)ompute | 1 alpha character (m/c) + 2 digits  |
| dc           | data center               | 2 alpha characters                  |

e.g. gre02-m01-dc, gre02-c01-dc, gre02-c02-dc

**vCenter Objects - Cluster Name**  
The naming standard is defined as: < locationCode >-< type >-< workloadDomainNumber >-< cluster-clusterNumber >

| Definition           | Description                | Details                             |
|----------------------|----------------------------|-------------------------------------|
| locationCode         |                            | 3 alpha characters (a-z) + 2 digits |
| type                 | (m)anagement or (c)compute | 1 alpha character (m/c) + 2 digits  |
| workloadDomainNumber | workload domain number     | 2 digits                            |
| clusterNumber        | cluster number             | 2 digits                            |

e.g. gre02-m01-mgmt01, gre02-c01-cluster01, gre02-c02-cluster01, gre02-c02-cluster02

**vCenter Objects - Host Name**  
The naming standard is defined as: < locationCode >< type >< hostNumber >

| Definition   | Description                    | Details                             |
|--------------|--------------------------------|-------------------------------------|
| locationCode |                                | 3 alpha characters (a-z) + 2 digits |
| type         | (mgt)anagement or (cmp)compute | 3 alpha character (mgt/cmp )        |
| hostNumber   | host number (001,002, etc.)    | 3 digits                            |

**Note:** For additional VI Workload, Each WLD host name will start like cmp101, cmp202 and each workload domain will have own consecutive hostname as below.  
e.g. gre02mgt001, gre02mgt002, gre02cmp001, gre02mgt002, gre02cmp001, gre02cmp101, gre02cmp102, gre02cmp103, gre02cmp201, gre02cmp202

**vCenter Objects - vSphere Distributed Switch Name**  
The naming standard is defined as: < locationCode >-< type >-vds< number >

| Definition   | Description                 | Details                             |
|--------------|-----------------------------|-------------------------------------|
| locationCode |                             | 3 alpha characters (a-z) + 2 digits |
| type         | (m)anagement or (c)ompute   | 1 alpha character (m/c) + 2 digits  |
| vds          | vsphere distributed switch  | 3 alpha characters + 2 digits       |
| number       | Represents sequential order | 2 digits                            |

e.g gre02-m01-vds01, mec09-c01-vds01

**vCenter Objects - vSphere Distributed Switch Port Groups (Compute only)**  
The naming standard is defined as: < locationCode >-< type >-< pg >-< networkType >

| Definition   | Description          | Details                             |
|--------------|----------------------|-------------------------------------|
| locationCode |                      | 3 alpha characters (a-z) + 2 digits |
| type         | (c)ompute            | 1 alpha character (c) + 2 digits    |
| pg           | port group           | 2 alpha characters                  |
| networkType  | vsan, vmtn, mgmt etc | 4 alpha characters + 2 digits       |

>Note: Port Group names for the management workload domain are hardcoded in vCF workflows and cannot be changed.

e.g gre02-c01-pg-vsan01

**vCenter Objects - vSAN Datastore Name**  
The naming standard is defined as: <locationCode><type><storageType>

| Definition           | Description                | Details                                 |
|----------------------|----------------------------|-----------------------------------------|
| locationCode         |                            | 3 alpha characters (a-z) + 2 digits     |
| type                 | (m)anagement or (c)compute | 1 alpha character (m/c) + 2 digits      |
| storageType (vsan)   | vsan                       | 4 alpha characters + 2 digits           |
| storageType (legacy) | nfs, lun                   | 3 alpha characters + 3 digits (001-256) |

e.g. gre02-m01-vsan01, gre02-m01-nfs001, mec09-c02-vsan01

**vCenter Objects - VMFS on FC (SAN) Datastore Name**  

> VMFS on FC can only be used as supplemental storage for the initial cluster in the management domain and consolidated workload domain, however it can be used as principal storage for subsequent clusters in these domains or in any VI workload domain.

The naming standard is defined as: < locationCode >-< type >-< storageType>< lunNumber >

| Definition         | Description                | Details                             |
|--------------------|----------------------------|-------------------------------------|
| locationCode       |                            | 3 alpha characters (a-z) + 2 digits |
| type               | (m)anagement or (c)compute | 1 alpha character (m/c) + 2 digits  |
| storageType (vmfs) | vmfs                       | 4 alpha characters + 2 digits       |
| lunNumber          | lun                        | 3 alpha characters + 2 digits       |

e.g. gre02-c01-vmfs01lun01, gre02-c02-vmfs02lun3

**vCenter Objects - VMFS on FC (SAN) Datastore Cluster Name**  
> VMFS on FC can only be used as supplemental storage for the initial cluster in the management domain and consolidated workload domain, however it can be used as principal storage for subsequent clusters in these domains or in any VI workload domain.

The naming standard for datastore cluster is defined as: < locationCode >-< type >-< cluster number >-< storage cluster >-< storageClass >-< replication flag >

| Definition       | Description                                        | Details                                                                     |
|------------------|----------------------------------------------------|-----------------------------------------------------------------------------|
| locationCode     |                                                    | 3 alpha characters (a-z) + 2 digits                                         |
| type             | (m)anagement or (c)compute                         | 1 alpha character (m/c) + 2 digits                                          |
| cluster number   | compute cluster number                             | 7 alpha characters + 2 digits                                               |
| storage cluster  | (s)torage (c)luster, type of container             | 2 alpha characters (sc)                                                     |
| storageClass     | storage tiers for specific SLA requirements        | `diamond`, `platinum`, `gold`, `silver`, `bronze` [as agreed with customer] |
| replication flag | indicates if storage replication is enabled or not | `repl` or `nonrepl`                                                         |

e.g. gre12-c01-cluster01-sc-silver-norepl

**vCenter Objects - Storage Policy**  
The default policy is defined with name “vSAN Default Storage Policy”  
The naming standard for hybrid vsan is defined as: < class >-< type >-< clusterNumber >-< ftt >-< str >-< cac >-< osr >-< fpr >-< iol >

| Definition      | Description                       | Details                                                                                |
|-----------------|-----------------------------------|----------------------------------------------------------------------------------------|
| class           | (hd) hybrid                       | 2 alpha characters (a-z)                                                               |
| type            | (m)anagement or (c)compute        | 1 alpha character (m/c) + 2 digits                                                     |
| clusterNumber   | cluster number                    | cluster + 2 digits                                                                     |
| ftt             | number of failures to tolerate    | 3 alpha characters + 3 digits (001<sup>2</sup>-003)                                    |
| str             | number of disk stripes per object | 3 alpha characters + 3 digits (001<sup>2</sup>-012)                                    |
| cac<sup>1</sup> | flash read cache reservation      | 3 alpha characters + 3 digits (000<sup>2</sup>-100)                                    |
| osr<sup>1</sup> | object space reservation          | 3 alpha characters + 3 digits (000<sup>2</sup>-100)                                    |
| fpr<sup>1</sup> | force provisioning                | 3 alpha characters + 3 digits (000<sup>2</sup>[no] or 001[yes])                        |
| iol<sup>1</sup> | iops limit for object             | 3 alpha characters + 3 digits (000<sup>2</sup>[no limit]-999[as agreed with customer]) |

<sup>1</sup> - optional  
<sup>2</sup> - default value  
e.g. hd-m01-cluster01-ftt001-str003, hd-c02-ftt002-str001-fpr001

The naming standard for all-flash vsan is defined as: < class >-< type >-< clusterNumber >-< ftt >-< ftm >-< str >-< cac >-< osr >-< fpr >-< iol >

| Definition      | Description                       | Details                                                                                |
|-----------------|-----------------------------------|----------------------------------------------------------------------------------------|
| class           | (af) all-flash                    | 2 alpha characters (a-z)                                                               |
| type            | (m)anagement or (c)ompute         | 1 alpha character (m/c) + 2 digits                                                     |
| clusterNumber   | cluster number                    | cluster + 2 digits                                                                     |
| ftt             | number of failures to tolerate    | 3 alpha characters + 3 digits (001<sup>2</sup>-003)                                    |
| ftm             | failure tolerance method          | 3 alpha characters + 3 digits (001<sup>2</sup>[raid1] or 005\006[raid5\6])             |
| str             | number of disk stripes per object | 3 alpha characters + 3 digits (001<sup>2</sup>-012)                                    |
| cac<sup>1</sup> | flash read cache reservation      | 3 alpha characters + 3 digits (000<sup>2</sup>-100)                                    |
| osr<sup>1</sup> | object space reservation          | 3 alpha characters + 3 digits (000<sup>2</sup>-100)                                    |
| fpr<sup>1</sup> | force provisioning                | 3 alpha characters + 3 digits (000<sup>2</sup>[no] or 001[yes])                        |
| iol<sup>1</sup> | iops limit for object             | 3 alpha characters + 3 digits (000<sup>2</sup>[no limit]-999[as agreed with customer]) |

<sup>1</sup> - optional  
<sup>2</sup> - default value  
e.g. af-m01-cluster01-ftt001-ftm001-str001

**vCenter Objects - Storage Policy for tag-based placement**

For external storage solution (SAN) in Workload Domain.<br>
The naming standard is defined as: < locationCode >-< storageType >-< type >-< clusterNumber >-< storageClass >-< replication flag >

| Definition       | Description                                        | Details                                                                     |
|------------------|----------------------------------------------------|-----------------------------------------------------------------------------|
| locationCode     |                                                    | alpha characters (a-z) + 2 digits                                           |
| storageType      | ie: vmfs                                           | 4 alpha characters (a-z)                                                    |
| type             | (m)anagement or (c)ompute                          | 1 alpha character (c)/(m) + 2 digits                                        |
| clusterNumber    | cluster number                                     | cluster + 2 digits                                                          |
| storageClass     | storage tiers for specific SLA requirements        | `diamond`, `platinum`, `gold`, `silver`, `bronze` [as agreed with customer] |
| replication flag | indicates if storage replication is enabled or not | `repl` or `nonrepl`                                                         |

e.g. gre12-vmfs-c01-cluster01-silver-nonrepl, gre12-vmfs-c01-cluster01-gold-repl

**vCenter Objects - vSphere Resource Pools**  
The naming standard is defined as: < locationCode >-< type >< workloadDomainNumber >-user-< poolType >  
The naming standard for tenant VM resouce pool in multi-tenant VCS is defined as:  
< locationCode >-< type >< workloadDomainNumber >-user-vm< clusterNumber >-< casTenantIdVar >

| Definition           | Description                             | Details                                        |
|----------------------|-----------------------------------------|------------------------------------------------|
| locationCode         |                                         | 3 alpha characters (a-z) + 2 digits            |
| type                 | (m)anagement or (c)compute              | 1 alpha character (m/c) + 2 digits             |
| workloadDomainNumber | Number of the workload domain           | 2 digits                                       |
| user                 | Indicator for user created objects      | 4 alpha characters                             |
| vm<sup>1</sup>       | Indicator for Customer virtual machines | 2 alpha characters                             |
| clusterNumber        | Number of the vSphere cluster           | 2 digits                                       |
| casTenantIdVar       | Name of the Tenant organization         | 3 alphanumeric characters (a-z,0-9) + 2 digits |
| poolType             | Resource pool type                      | 4 alpha characters (a-z) + 2 digits            |

<sup>1</sup> - applicable only for customer/tenant resource pools

e.g. gre02-c01-user-edge01, gre02-c01-user-mgmt01,gre02-c01-user-vm01-nx301

**vCenter Objects - DRS Groups & Rules (Multiple Availability Zones - Stretched Cluster)**

| Definition                  | Description                                                 | Details                                              |
|-----------------------------|-------------------------------------------------------------|------------------------------------------------------|
| locationCode/locationCodeDr |                                                             | 3 alpha characters (a-z) + 2 digits                  |
| drs                         | DRS object                                                  | written using lowercase letters                      |
| type                        | (m)anagement or (c)compute                                  | 1 alpha character (m/c) + 2 digits                   |
| groupType                   | one of the following options: hostgroup, vmgroup,vmhostrule | written using lowercase letters followed by 2 digits |

Example of DRS host groups in the 1st Compute Workload Domain, 1st cluster:

- mec09-c01-drs-hostgroup01 (group containing hosts in mec09 Availability Zone)
- mec10-c01-drs-hostgroup01 (group containing hosts in mec10 Availability Zone)

Example of a DRS rule combining ESXi hosts and VMs in mec09 Availability zone (1st Compute Workload Domain, first Cluster):

- mec09-c01-drs-vmhostrule01

**vCenter Objects - vSphere Native Key Provider**  
Naming standard is defined as < vcsName >-NKP-< instanceNumber >

| Definition     | Description                | Details                             |
|----------------|----------------------------|-------------------------------------|
| vcsName        | vCenter Server name on which Key Provider is configured | < locationCode > vcs < 3 digits > |
| NKP            | service identification - Native Key Provider | fixed value with capital letters |
| instanceNumber | Instance number identification | 3 digits |

eg: gre28vcs002-NKP-001

**Single-Sign-On Site Name**  
The naming standard is defined as: < locationCode >

| Definition   | Description | Details                             |
|--------------|-------------|-------------------------------------|
| locationCode |             | 3 alpha characters (a-z) + 2 digits |

e.g. gre02

**SDDC Manager - Host Pool Name**  
The naming standard is defined as: < locationCode >-< networkpool >

| Definition   | Description | Details                             |
|--------------|-------------|-------------------------------------|
| locationCode |             | 3 alpha characters (a-z) + 2 digits |
| networkpool  | networkpool | 11 alpha characters                 |

e.g. gre02-networkpool

**SDDC Manager objects - Network Pool**

vMotion-only network pool is dedicated for converged storage-based WD. Naming standard is defined as: < locationCode >-< typeOfTraffic >-< networkpool >

| Definition    | Description                  | Details                             |
|---------------|------------------------------|-------------------------------------|
| locationCode  |                              | 3 alpha characters (a-z) + 2 digits |
| typeOfTraffic | type of traffic, ie: vmotion | alpha characters only               |
| networkpool   | networkpool                  | 11 alpha characters                 |

e.g. gre12-vmotiononly-networkpool

## VMware Aria Automation elements

**VMware Aria Automation objects - Parent Organization Name**  
The naming standard is defined as: < customerCode >

| Definition   | Description         | Details                             |
|--------------|---------------------|-------------------------------------|
| customerCode | Customer short name | 3 alpha characters (a-z) upper case |

e.g. CIS, SIE

**VMware Aria Automation objects - Tenant Name**  
The naming standard is defined as: < tenantCode >< tenantNumber >

| Definition   | Description                 | Details                     |
|--------------|-----------------------------|-----------------------------|
| tenantCode   | Tenant code                 | 3 alpha characters (a-z)    |
| tenantNumber | Represents sequential order | 2 digits starting from _01_ |

e.g. akz01, sie01, nx301

**VMware Aria Automation objects - Projects**  
The naming standard is defined as: < projectCode >< number >

| Definition  | Description                 | Details                                  |
|-------------|-----------------------------|------------------------------------------|
| projectCode | Project short name          | 3 alpha characters (a-z)                 |
| number      | represents sequential order | 3 digits starting from _001_ up to _999_ |

e.g. prd001, fin001, dev001

**VMware Aria Automation objects - Cloud Accounts**  
The naming standard is defined as: < locationCode >vcs< number >

| Definition   | Description                            | Details                             |
|--------------|----------------------------------------|-------------------------------------|
| locationCode |                                        | 3 alpha characters (a-z) + 2 digits |
| vcs          | vCenter object                         | written using lowercase letters     |
| number       | Represents vCenter number (001 or 002) | 3 digits starting from _001_        |

e.g. gre32vcs001, gre02vcs002

**VMware Aria Automation objects - Cloud Zones**  
The naming standard is defined as: < locationCode >< number >

| Definition   | Description                            | Details                             |
|--------------|----------------------------------------|-------------------------------------|
| locationCode |                                        | 3 alpha characters (a-z) + 2 digits |
| number       | Represents Cloud Zone sequential order | 2 digits starting from _01_         |

e.g. mec4901, gre3201

**VMware Aria Automation objects - Image Mappings**  
The naming standard is defined as: atos-< imageName >

| Definition | Description       | Details                               |
|------------|-------------------|---------------------------------------|
| atos       | Global image name | written using lowercase letters       |
| imageName  | Name of the image | 5-7 alphanumeric characters (a-z,0-9) |

e.g. atos-rhel7, atos-sles15, atos-win2019

**VMware Aria Automation objects - Flavour Mappings**  
The naming standard is defined as: < flavorName >

| Definition | Description         | Details                                        |
|------------|---------------------|------------------------------------------------|
| flavorName | Name of the flavour | single word (without spaces) indicating flavor |

e.g. Small, Large, XSmall

**VMware Aria Automation objects - Network Profile**  
The naming standard is defined as: < tenantName >< description >< number >

| Definition  | Description                       | Details                                  |
|-------------|-----------------------------------|------------------------------------------|
| tenantName  | Tenant name                       | 3 alpha characters (a-z) + 2 digits      |
| description | Short network name (web, app, db) | 3 alpha characters (a-z)                 |
| number      | Represents sequential order       | 3 digits starting from _001_ up to _999_ |

e.g. akz01web001, sie01app001, nx301db001

**VMware Aria Automation objects - Storage Profile**  
The naming standard is defined as: cluster< clusterNumber >-< type >

| Definition    | Description                   | Details                         |
|---------------|-------------------------------|---------------------------------|
| cluster       | Cluster object                | written using lowercase letters |
| clusterNumber | Number of the vSphere cluster | 2 digits                        |
| type          | Represents storage class      | gold                            |

e.g. cluster01-gold, cluster02-gold

**VMware Aria Automation objects - Storage Profile for VMFS on FC (SAN)**<br>
The naming standard is defined as: cluster< clusterNumber >-< type >-< replication flag >

| Definition       | Description                                        | Details                                                                     |
|------------------|----------------------------------------------------|-----------------------------------------------------------------------------|
| cluster          | Cluster object                                     | written using lowercase letters                                             |
| clusterNumber    | Number of the vSphere cluster                      | 2 digits                                                                    |
| type             | Represents storage class                           | `diamond`, `platinum`, `gold`, `silver`, `bronze` [as agreed with customer] |
| replication flag | indicates if storage replication is enabled or not | `repl` or `nonrepl`                                                         |

e.g. cluster01-silver-nonrepl, cluster02-gold-repl

**VMware Aria Automation objects - Blueprints**  
The naming standard for Cloud Assembly blueprint is defined as: Operational Blueprint [ < projectName > ]  
The naming standard for Service Broker blueprint is defined as: Deploy virtual machine [ < projectName > ]

e.g. Deploy virtual machine [prd001], Operational Blueprint [dev001]

**VMware Aria Automation objects - Content Sources**  
The naming standard is defined as: < customerCode >< number > blueprints

| Definition   | Description                               | Details                             |
|--------------|-------------------------------------------|-------------------------------------|
| customerCode | Customer short name                       | 3 alphanumeric characters (a-z,0-9) |
| number       | Represents extensibility sequential order | 3 digits starting from _001_        |

e.g. nx3001 blueprints, nx3002 blueprints

**VMware Aria Automation objects - Custom Role**  
The naming standard is defined as: < tenantName >< roleName >

| Definition | Description                 | Details                                             |
|------------|-----------------------------|-----------------------------------------------------|
| tenantName | Tenant name                 | 3 alpha characters (a-z) + 2 digits                 |
| roleName   | Short descriptive role name | alpha characters (a-z) starting from capital letter |

e.g. akz01DevSecOps, nx001ReadOnly

**VMware Aria Automation objects - Policy Definitions**  
The naming standard is defined as: {tenantName}-day2actions-users-< policyType >

| Definition | Description                            | Details                             |
|------------|----------------------------------------|-------------------------------------|
| tenantName | Tenant name                            | 3 alpha characters (a-z) + 2 digits |
| policyType | 2day actions type (standard, optional) | alpha characters (a-z)              |

e.g. akz01DevSecOps, nx001ReadOnly

## Customer specific variables

Customer specific variables are limited to:

- customerCode
- locationCode
- networkMgmt
  - cidr
  - gw
  - vlan
  - netmask
- networkVmotionCidr
  - cidr
  - gw
  - vlan
  - netmask
- networkVsanCidr
  - cidr
  - gw
  - vlan
  - netmask
- networkVxlan
  - vlan

### Customer code

Customer code [ customerCode ] consists of three lower-case letters.
> Example: **ato** can represent Atos customer code

### Location code

Location code [ locationCode ] consists of three lower-case letters followed by two numbers.
> Example: **mec01** can represent first POD in Mechelen location

### Management Network

Management network configuration consists of four variables:

- CIDR: first three octets of the management address
- Gateway: fourth octet number
- VLAN: VLAN used by that network
- Netmask: the subnet mask of the related network

Example:

```yaml
# Management network details
networkMgmt:
  cidr: 99.99.99
  gw: 1
  vlan: 99
  netmask: 24
```

### vMotion Network

vMotion network configuration consists of four variables:

- CIDR: first three octets of the management address
- Gateway: fourth octet number
- VLAN: VLAN used by that network
- Netmask: the subnet mask of the related network

Example:

```yaml
# vMotion network details
networkVmotion:
  cidr: 99.99.99
  gw: 1
  vlan: 99
  netmask: 24
```

### vSAN Network

vSAN network configuration consists of four variables:

- CIDR: first three octets of management address
- Gateway: fourth octet number
- VLAN: VLAN used by that network
- Netmask: the subnet mask of the related network

Example:

```yaml
# vSAN network details
networkVsan:
  cidr: 99.99.99
  gw: 1
  vlan: 99
  netmask: 24
```

### VXLAN Network

VXLAN network configuration consists of four variables:

- CIDR: first three octets of management address
- Gateway: fourth octet number
- VLAN: VLAN used by that network
- Netmask: the subnet mask of the related network

Example:

```yaml
# VXLAN network details
networkVxlan:
  cidr: 99.99.99
  gw: 1
  vlan: 99
  netmask: 24
```

## Customer Infra Vars

The naming standard for customInfraVars.yml is defined as follows:

### Component names

| Component          | Variable                          | Naming standard                                                 | Example               | Notes                      |
|--------------------|-----------------------------------|-----------------------------------------------------------------|-----------------------|----------------------------|
| NSX-T Edge Node    | edgeNode.spec.name                | < locationCode >ecn< ecnNumber >edg< edgNumber >                | gre26ecn01edg01       |                            |
| NSX-T Edge Node Bridge | N/A | < locationCode >ecn< ecnNumber >< tenantName >brg< customerBridgeNumber > | gre26ecn01vx601brg01 | brg is a constant value   |
| NSX-T Edge Cluster | edgeCluster.spec.name             | < locationCode >ecn< ecnNumber >                                | gre26ecn01            |                            |
| T0 template router | logicalRouterT0template.spec.name | < locationCode >ecn< ecnNumber >lrt001                          | gre26ecn01lrt001      | lrt001 is a constant value |
| T0 VRF router      | logicalRouterT0vrf.spec.name      | < locationCode >ecn< ecnNumber >< tenantName >vrf< vrfNumber >  | gre26ecn01vx601vrf01  |                            |
| T1 router          | logicalRouterT1.spec.name         | < locationCode >ecn< ecnNumber >< tenantName >lrt1< lrtNumber > | gre26ecn01vx601lrt101 | lrt1 is a constant value   |
| NSX-T segment      | segmentNsx.spec.name              | < tenantName >< description >                                   | vx601AppTier          |                            |

### Uplink segment names

| Component                 | Variable                                            | Naming standard                                | Example             | Notes                        |
|---------------------------|-----------------------------------------------------|------------------------------------------------|---------------------|------------------------------|
| T0 template router uplink | logicalRouterT0template.spec.uplinkVlanSegment.name | < locationCode >ecn< ecnNumber >vtzTrunk       | gre26ecn01vtzTrunk  | vtzTrunk is a constant value |
| T0 VRF router uplink      | logicalRouterT0vrf.spec.uplinkVlanSegment.name      | < locationCode >< tenantName >Uplink< vlanId > | gre26vx601Uplink369 |                              |

### Field definitions

- ecnNumber - two-digit incremental value representing cluster number, where ecn stands for "edge cluster number"
- edgNumber - two-digit incremental value representing edge node number
- customerBridgeNumber - two-digit incremental value representing Customer-specific Edge Bridge instance
- lrt001 - fixed value, stands for "logical router t0" and number 01
- vrfNumber - two-digit incremental value representing VRF number
- lrt1 - fixed value, stands for "logical router t1"
- lrtNumber - two-digit incremental value representing T1 router number
- vtzTrunk - fixed value, stands for VLAN Transport Zone Trunk
- vlanId - VLAN ID used for Transport Zone between Edges and L3 routing device for corresponding VRF

| Date       | Issue    | Author            |   TOS   | Description                                                                                                      |
|------------|----------|-------------------|:-------:|------------------------------------------------------------------------------------------------------------------|
| 2019-11-13 |          | Piotr Lewandowski |         | added Workload Domain and Port Groups, updated vCenter objects to accommodate multiple clusters/workload domains |
| 2021-02-04 | DHC-943  | Kacper Kuliberda  |         | Move variables.md into this document, fix formatting                                                             |
| 2021-05-12 | DHC-1981 | Marcin Kujawski   |         | Update naming for TOS 1.3 and multi-tenancy variables                                                            |
| 2022-03-02 | DHC-4201 | Lukasz Bienkowski |         | Added Customer Infra Vars section for omniTemplate network components                                            |
| 2022-04-08 | DHC-4404 | Maciej Losek      |         | Added VMFS on FC storage type and versionMatrix specific abbrevations                                            |
| 2023-02-08 |          | Maikal Kumar      | VCS 1.7 | updated naming convention of vSAN Datastore Name,VMFS on FC (SAN) Datastore Name                                 |
|2024-02-09|VCS-12032|Adam Wieczorek| VCS 2.0  |Added naming convention for Native Key Provider|
| 2024-06-03 | VCS-4257 | Pawel Zurawski | VCS 2.0 | Added naming convention for Edge Bridges |
| 2025-02-27 | VCS-15268 | Mariusz Stanek | VCS 2.0 | VRA naming changed and bil, git, avr removed  |
