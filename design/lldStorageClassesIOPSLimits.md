# Storage Classes IOPS Limits LLD

## Changelog

| Date       | TOS     | Issue       | Author(s)                    | Descrition                                                                 |
|------------|---------|-------------|------------------------------|----------------------------------------------------------------------------|
| 2021-02-02 |         |             | Vishnu Panchal               | Initial Draft                                                              |
| 2021-24-08 |         | DHC-2606    | Abhijeet Janwalkar           | Added Reporting, Monitoring and 2nd Day Operations                         |
| 2021-26-10 |         | DHC-3325    | Shilpa Arote                 | updated naming convention of storage policy to append storage profile name |
| 2021-22-11 |         | DHC-3332    | Madhavi Rane                 | Removed 2nd Day action for Cloud.vSphere.Disk resource                     |
| 2021-12-08 |         | DHC-3692    | Madhavi Rane                 | Updated OS disk's allowed IOPs Limit to >=250 from >=1000                  |
| 2022-02-17 |         | DHC-3936    | Shilpa Arote                 | updated creation of high Iops storage profile creation                     |
| 2022-03-07 |         | DHC-4317    | Madhavi Rane                 | Added high Iops profile as allowed profile for OS disk                     |
| 2022-07-18 |         | CESDHC-232  | Margo Piliukh                | Added Default SPBM profile                                                 |
| 2022-12-05 |         | CESDHC-5071 | Added VFMS on FC information | Adam Wieczorek                                                             |
| 2023-02-20 | VCS 1.7 | CESDHC-5898 | Vishnu Panchal               | Additional WLD changes                                                     |

## Purpose

The purpose of this document is to provide detailed design and architectural guidance required to implement Storage classes and IOPS limits in VCS in accordance with Atos Global Delivery standards and portfolio services.

## Design Decisions

| Decision ID | Design description                                                                                                                       | Requirement Source   | Requirement Level |
|-------------|------------------------------------------------------------------------------------------------------------------------------------------|----------------------|-------------------|
| D001        | Common set of storage profiles/classes/policies for all customers in a shared environment across all tenants                             | vSAN limitation      | MUST              |
| D002        | Default storage classes should be customizable as needed in non shared environments                                                      | Customer Requirement | MUST              |
| D003        | Storage Policy/classes will be applied at VM level or at individual disk level (day 2 action)                                            | Customer Requirement | MUST              |
| D004        | VRA Cloud IOPS limitation setting per VM will not be used                                                                                | VCS Design           | MUST              |
| D005        | One to One mapping between storage profile and VSAN storage policy. A storage policy will not be used in multiple cloud storage profiles | VCS Design           | MUST              |
| D006        | Default failures to tolerate (ftt) is ftt=001 & stripe width (str) is str=001                                                            | VCS Default          | MUST              |
| D007        | For all-flash vSAN clusters, default fault tolerance method (ftm) is ftm=005 (RAID5)                                                     | VCS Design           | MUST              |
| D008        | For hybrid vSAN clusters, default fault tolerance method (ftm) is ftm=001 (RAID1)                                                        | vSAN limitation      | MUST              |
| D009        | Separate storage policies are created for each cluster                                                                                   | VCS Design           | MUST              |

Deviation from the default configuration will have impact on capacity utilization. Proper capacity & performance requirement planning should be done before considering different ftt, ftm & stripe width at the time of deployment.

## IOPS Normalization

Storage classes and IOPS limitations are based on I/O values normalised to 32KB (IOPS size can vary from 4KB to 1MB).
A typical 64 KB IO will be treated as 2 IOPS of 32KB in size. There are no dedicated IOPS limits per customer. Each customer will use standard storage classes with predefined IOPS maximums in a shared environment. IOPS limits based on disk size is not possible in vSAN as in converged /traditional storage systems. Storage class represents maximum IO requests a vm can send to underlying storage regardless of any additional resources being available. IO requests above specified limit of a storage class are throttled.

## Storage Classes

Standard storage classes in VCS are based on IOPS limitations implemented at vSAN level. VCS storage is 100% shared and doesn't guarantee IOPS per VM / object / customer. Only maximum IOPS value can be set up which limits IOPS operations based on that value for a VM. There's no guarantee of specific response time/SLA for IOs.

Standard IOPS values would be used in build with option to increase/change at install time depending on the contract. This is not "per TB" - it's a limitation per VM virtual disk. Customer will have option to change the storage class for individual disks of a VM in day 2 operations.

List of VCS standard storage classes with respective IOPS limitations.

| Storage class | IOPS limit | Storage_tag | Failures to tolerate | Stripe width | Fault tolerance method |
|---------------|------------|-------------|----------------------|--------------|------------------------|
| Bronze        | 100        | bronze      | 1                    | 1            | RAID5                  |
| Silver        | 250        | silver      | 1                    | 1            | RAID5                  |
| Gold          | 1000       | gold        | 1                    | 1            | RAID5                  |
| Platinum      | 3000       | platinum    | 1                    | 1            | RAID5                  |
| Diamond       | 6000       | diamond     | 1                    | 1            | RAID5                  |
| Database      | 0          | database    | 1                    | 1            | RAID1                  |
| Default       | 0          | default     | 1                    | 1            | RAID1                  |

Each storage class will have different pricing and it will be customers decision what class/tiers best suits for a VM.
Single storage class can be applied for all disks of a VM. It is also possible to setup different storage class for each individual disks of a VM.

Customer will have ability to change the IOPS limit definition for each storage class or create additional storage classes with required IOPS values.

Note-

- If RAID1 ftm is required, it can be created as customization on all-flash cluster.
- For hybrid or "all-flash workloads with less than 5 nodes", RAID1 will be default fault tolerance method.
- "Gold" is highest storage class on hybrid vSAN clusters.
- OS disks by default have "Gold" class on hybrid and gold or above on all-flash clusters.
- Customer can select storage profile with IOPs Limit >= 250 or IOPs Limit = 0 (i.e.Unlimited IOPs) from available storage profiles during VM provisioning.
- 2nd Day action "Change Disk Storage Class" allows user to select storage profile with IOPs Limit >= 250 or IOPs Limit = 0 (i.e.Unlimited IOPs) from available storage profiles for OS disk.
- Database is the high iops storage profile used for database. It has IOPs limit = 0 (i.e. unlimited IOPs)
- When the Default storage policy is configured as a default, a different number of SPBM policies will be created in vCenter depending on the type of the environment. For Active-Passive or Standalone cluster one policy will be created; for Active-Active cluster three default policies will be created (dual-site mirroring, preferred site, and non-preferred site).

### VMFS on FC

Storage classes approach for VMFS on FC (so called Converged Storage) shares some similarities with vSAN in terms of storage classes naming convention used, however IOPS limits (and other characteristics ie. RAID level, etc) are set on storage array level and are customer specific. Therefore there is no general mapping between Storage Class and IOPS limit, RAID levels, etc for VCS.

Apart from Storage Class VMFS on FC introduces also replication capability indicator which combined with storage class defines presented storage capabilities. Storage replication information indicates whether given storage is replicated to the remote site (for A/P DR solution) or not.

For the details of Storage Policy naming convention for VMFS on FC please see [Naming Convention LLD](namingConvention.md)

## Storage Profile

Storage profiles in vRA links to the storage policies at the vCenter level. Combination of tier and storage class with matching storage profile is way to identify the type of underlying storage & its capability. Tags are then matched against provisioning service request constraints to select desired storage policy in deployment.

Storage profiles are organized under cloud-specific regions/account. One cloud account might have multiple regions, with multiple storage profiles under each.

Thin provisioning is used for configuring storage profiles and storage policies. VCS Standard Storage Profiles will be created for each clusters in every workload domain in a region/account. storage profiles will not have datastore binding.

| Tag          | Scope  | Description                       |
|--------------|--------|-----------------------------------|
| CloudStorage | shared | cloudstorage:cluster-storageclass |

e.g.: cluster01-bronze, cluster02-gold etc..

### VMFS on FC

As the general approach is similar as with vSAN, since VMFS on FC can use multiple datastore with different capabilities, there is additional mapping configured within Storage Profiles in vRA. Apart from mapping Storage Profile to vCenter Storage Policy object it is also mapped to vSphere Datastore CLuster which is compliant with given vSphere Storage Policy. Additional Capability tag is introduced for VMFS on FC. Each vRA Storage Profile has 2 Capability tags - `cloudstorage` and `StorageReplication`

For the details of Storage Profile naming convention for VMFS on FC please see [Naming Convention LLD](namingConvention.md)

## Policy Naming Convention

The naming standard for hybrid/ all-flash vsan is defined as: class-WorkloadDomain-Cluster-ftt-str-ftm-cac-osr-fpr-iol_cluster-storageclass

| Definition           | Description                       | Details                                                             |
|----------------------|-----------------------------------|---------------------------------------------------------------------|
| class                | (hd) hybrid af (all-flash)        | 2 alpha characters (a-z)                                            |
| WorkloadDomain       | (m)anagement or (c)compute        | 1 alpha character (m/c) + 2 digits                                  |
| Cluster              | cluster number                    | cluster + 2 digits                                                  |
| ftt                  | number of failures to tolerate    | 3 alpha characters + 3 digits (001<sup>2</sup>-003)                 |
| ftm                  | Fault tolerance method R5/R1      | 3 alpha characters + 3 digits (005<sup>2</sup>001 )                 |
| str                  | number of disk stripes per object | 3 alpha characters + 3 digits (001<sup>2</sup>-012)                 |
| cac<sup>1</sup>      | flash read cache reservation      | 3 alpha characters + 3 digits (000<sup>2</sup>-100)                 |
| osr<sup>1</sup>      | object space reservation          | 3 alpha characters + 3 digits (000<sup>2</sup>-100)                 |
| fpr<sup>1</sup>      | force provisioning                | 3 alpha characters + 3 digits (000<sup>2</sup>[no] or 001[yes])     |
| iol<sup>1</sup>      | iops limit for object             | 3 alpha characters + 3/4 digits (100-6000[as agreed with customer]) |
| cluster-storageclass | vRA storage profile name          | cluster + 2 digits - storageclass( standard or customer provided)   |

<sup>1</sup> - optional  
<sup>2</sup> - default value

e.g. hd-c01-cluster01-ftt001-ftm001-str001-iol100_cluster01-bronze, af-c01-cluster02-ftt001-ftm005--str001-iol250_cluster02-silver

Note- For additional WLDs in VCS, same storage policy naming convention should be followed. Eg. hd-c02-cluster01-ftt001-ftm001-str001-iol100_cluster01-bronze, af-c03-cluster01-ftt001-ftm005--str001-iol250_cluster01-silver.

## Object Mapping

Storage policies created at vCenter level are mapped to respective storage profiles in vRA Cloud. Storage profiles will have logic to use appropriate storage class/datastore based on user inputs /choices in deployment.  
Mapping of storage classes, profiles, clusters, tags etc.. is as below. Policies are indicative and can grow as per customer requirement & no. of clusters in workload domain.

### All-flash Clusters

| Storage Class | Cluster       | Storage_Tag ("Storage Class" in catalog) | Storage profile    | Constraint tag                  | Storage Policy in vCenter                                        | IOPS |
|---------------|---------------|------------------------------------------|--------------------|---------------------------------|------------------------------------------------------------------|------|
|               |               |                                          |                    |                                 |                                                                  |      |
| Bronze        | c01-cluster01 | bronze                                   | cluster01-bronze   | cloudstorage:cluster01-bronze   | af-c01-cluster01-ftt001-ftm005-str001-iol100_cluster01-bronze    | 100  |
|               | c01-cluster02 | bronze                                   | cluster02-bronze   | cloudstorage:cluster02-bronze   | af-c01-cluster02-ftt001-ftm005-str001-iol100_cluster01-bronze    | 100  |
|               | 🠋            |                                          | .                  |                                 |                                                                  |      |
| Silver        | c01-cluster01 | silver                                   | cluster01-silver   | cloudstorage:cluster01-silver   | af-c01-cluster01-ftt001-ftm005-str001-iol250_cluster01-silver    | 250  |
|               | c01-cluster02 | silver                                   | cluster02-silver   | cloudstorage:cluster02-silver   | af-c01-cluster02-ftt001-ftm005-str001-iol250_cluster02-silver    | 250  |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |
| Gold          | c01-cluster01 | gold                                     | cluster01-gold     | cloudstorage:cluster01-gold     | af-c01-cluster01-ftt001-ftm005-str001-iol1000_cluster01-gold     | 1000 |
|               | c01-cluster02 | gold                                     | cluster02-gold     | cloudstorage:cluster02-gold     | af-c01-cluster02-ftt001-ftm005-str001-iol1000_cluster02-gold     | 1000 |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |
| Platinum      | c01-cluster01 | platinum                                 | cluster01-platinum | cloudstorage:cluster01-platinum | af-c01-cluster01-ftt001-ftm005-str001-iol3000_cluster01-platinum | 3000 |
|               | c01-cluster02 | platinum                                 | cluster02-platinum | cloudstorage:cluster02-platinum | af-c01-cluster02-ftt001-ftm005-str001-iol3000_cluster02-platinum | 3000 |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |
| Diamond       | c01-cluster01 | diamond                                  | cluster01-diamond  | cloudstorage:cluster01-diamond  | af-c01-cluster01-ftt001-ftm005-str001-iol6000_cluster01-diamond  | 6000 |
|               | c01-cluster02 | diamond                                  | cluster02-diamond  | cloudstorage:cluster02-diamond  | af-c01-cluster02-ftt001-ftm005-str001-iol6000_cluster02-diamond  | 6000 |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |
| Default       | c01-cluster01 | default                                  | cluster01-default  | cloudstorage:cluster01-default  | af-c01-cluster01-ftt001-ftm005-str001-iol0_cluster01-default     | 0    |
|               | c01-cluster02 | default                                  | cluster02-default  | cloudstorage:cluster02-default  | af-c01-cluster02-ftt001-ftm005-str001-iol0_cluster02-default     | 0    |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |

### Hybrid Clusters

| Storage Class | Cluster       | Storage_Tag ("Storage Class" in catalog) | Storage profile  | Constraint tag                | Storage Policy in vCenter                                     | IOPS |
|---------------|---------------|------------------------------------------|------------------|-------------------------------|---------------------------------------------------------------|------|
|               |               |                                          |                  |                               |                                                               |      |
|               | c01-cluster01 | bronze                                   | cluster01-bronze | cloudstorage:cluster01-bronze | hd-c01-cluster01-ftt001-ftm001-str001-iol100_cluster01-bronze | 100  |
| Bronze        | c01-cluster02 | bronze                                   | cluster02-bronze | cloudstorage:cluster02-bronze | hd-c01-cluster02-ftt001-ftm001-str001-iol100_cluster02-bronze | 100  |
|               | 🠋            |                                          | .                |                               |                                                               | 100  |
|               | c01-cluster01 | silver                                   | cluster01-silver | cloudstorage:cluster01-silver | hd-c01-cluster01-ftt001-ftm001-str001-iol250_cluster01-silver | 250  |
| Silver        | c01-cluster02 | silver                                   | cluster02-silver | cloudstorage:cluster02-silver | hd-c01-cluster02-ftt001-ftm001-str001-iol250_cluster02-silver | 250  |
|               | 🠋            |                                          |                  |                               |                                                               |      |
|               | c01-cluster01 | gold                                     | cluster01-gold   | cloudstorage:cluster01-gold   | hd-c01-cluster01-ftt001-ftm001-str001-iol1000_cluster01-gold  | 1000 |
| Gold          | c01-cluster02 | gold                                     | cluster02-gold   | cloudstorage:cluster02-gold   | hd-c01-cluster02-ftt001-ftm001-str001-iol1000_cluster02-gold  | 1000 |
|               | 🠋            |                                          |                  |                               |                                                               |      |

### Nodes in cluster <5 | Customer requirement = RAID1

| Storage Class | Cluster       | Storage_Tag ("Storage Class" in catalog) | Storage profile    | Constraint tag                  | Storage Policy in vCenter                                        | IOPS |
|---------------|---------------|------------------------------------------|--------------------|---------------------------------|------------------------------------------------------------------|------|
|               |               |                                          |                    |                                 |                                                                  |      |
|               | c01-cluster01 | bronze                                   | cluster01-bronze   | cloudstorage:cluster01-bronze   | af-c01-cluster01-ftt001-ftm001-str001-iol100_cluster01-bronze    | 100  |
| Bronze        | c01-cluster02 | bronze                                   | cluster02-bronze   | cloudstorage:cluster02-bronze   | af-c01-cluster02-ftt001-ftm001-str001-iol100_cluster02-bronze    | 100  |
|               | 🠋            |                                          | .                  |                                 |                                                                  |      |
|               | c01-cluster01 | silver                                   | cluster01-silver   | cloudstorage:cluster01-silver   | af-c01-cluster01-ftt001-ftm001-str001-iol250_cluster01-silver    | 250  |
| Silver        | c01-cluster02 | silver                                   | cluster02-silver   | cloudstorage:cluster02-silver   | af-c01-cluster02-ftt001-ftm001-str001-iol250_cluster02-silver    | 250  |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |
|               | c01-cluster01 | gold                                     | cluster01-gold     | cloudstorage:cluster01-gold     | af-c01-cluster01-ftt001-ftm001-str001-iol1000_cluster01-gold     | 1000 |
| Gold          | c01-cluster02 | gold                                     | cluster02-gold     | cloudstorage:cluster02-gold     | af-c01-cluster02-ftt001-ftm001-str001-iol1000_cluster02-gold     | 1000 |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |
|               | c01-cluster01 | platinum                                 | cluster01-platinum | cloudstorage:cluster01-platinum | af-c01-cluster01-ftt001-ftm001-str001-iol3000_cluster01-platinum | 3000 |
| Platinum      | c01-cluster02 | platinum                                 | cluster02-platinum | cloudstorage:cluster02-platinum | af-c01-cluster02-ftt001-ftm001-str001-iol3000_cluster02-platinum | 3000 |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |
|               | c01-cluster01 | diamond                                  | cluster01-diamond  | cloudstorage:cluster01-diamond  | af-c01-cluster01-ftt001-ftm001-str001-iol6000_cluster01-diamond  | 6000 |
| Diamond       | c01-cluster02 | diamond                                  | cluster02-diamond  | cloudstorage:cluster02-diamond  | af-c01-cluster02-ftt001-ftm001-str001-iol6000_cluster02-diamond  | 6000 |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |
|               | c01-cluster01 | database                                 | cluster01-database | cloudstorage:cluster01-database | af-c01-cluster01-ftt001-ftm001-str001-iol0_cluster01-database    | 0    |
| Database      | c01-cluster02 | database                                 | cluster02-database | cloudstorage:cluster02-database | af-c01-cluster02-ftt001-ftm001-str001-iol0_cluster02-database    | 0    |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |
|               | c01-cluster01 | Default                                  | cluster01-default  | cloudstorage:cluster01-default  | af-c01-cluster01-ftt001-ftm001-str001-iol0_cluster01-default     | 0    |
| Default       | c01-cluster02 | default                                  | cluster02-default  | cloudstorage:cluster02-default  | af-c01-cluster02-ftt001-ftm001-str001-iol0_cluster02-default     | 0    |
|               | 🠋            |                                          |                    |                                 |                                                                  |      |

### Active-Active cluster default storage policies

For Active-Active cluster, additional storage policies are created on vCenter level to reflect the placement preferences. Only one storage policy is mapped to the respective storage profile in vRA Cloud.

| Storage Policy in vCenter                                                         | Site Disaster Tolarance for Placement                 | vRA Cloud mapping |
|-----------------------------------------------------------------------------------|-------------------------------------------------------|-------------------|
| af-c01-cluster01-ftt001-ftm001-str001-iol0_cluster01-default                      | Dual site mirroring (stretched cluster)               | cluster01-default |
| af-{{ locationCode }}-c01-cluster01-ftt001-ftm001-str001-iol0_cluster01-default   | None - keep data on Preferred (stretched cluster)     | NONE              |
| af-{{ locationCodeDr }}-c01-cluster01-ftt001-ftm001-str001-iol0_cluster01-default | None - keep data on Non-preferred (stretched cluster) | NONE              |

### VMFS on FC

| Storage Class | Replication | Cluster       | Storage_Tag ("Storage Class" in catalog) | Storage profile            | Constraint tag                                              | Storage Policy in vCenter                            |
|---------------|-------------|---------------|------------------------------------------|----------------------------|-------------------------------------------------------------|------------------------------------------------------|
|               |             |               |                                          |                            |                                                             |                                                      |
| Bronze        | repl        | c01-cluster01 | bronze                                   | cluster01-bronze-repl      | cloudstorage:cluster01-bronze<br />StorageReplication:yes   | `<locationCode>`-vmfs-c01-cluster01-bronze-repl      |
| Bronze        | nonrepl     | c01-cluster01 | bronze                                   | cluster01-bronze-nonrepl   | cloudstorage:cluster01-bronze<br />StorageReplication:no    | `<locationCode>`-vmfs-c01-cluster01-bronze-nonrepl   |
| Bronze        | repl        | c01-cluster02 | bronze                                   | cluster02-bronze-repl      | cloudstorage:cluster02-bronze<br />StorageReplication:yes   | `<locationCode>`-vmfs-c01-cluster02-bronze-repl      |
| Bronze        | nonrepl     | c01-cluster02 | bronze                                   | cluster02-bronze-nonrepl   | cloudstorage:cluster02-bronze<br />StorageReplication:no    | `<locationCode>`-vmfs-c01-cluster02-bronze-nonrepl   |
|               |             | 🠋            |                                          |                            |                                                             |                                                      |
| Silver        | repl        | c01-cluster01 | silver                                   | cluster01-silver-repl      | cloudstorage:cluster01-silver<br />StorageReplication:yes   | `<locationCode>`-vmfs-c01-cluster01-silver-repl      |
| Silver        | nonrepl     | c01-cluster01 | silver                                   | cluster01-silver-nonrepl   | cloudstorage:cluster01-silver<br />StorageReplication:no    | `<locationCode>`-vmfs-c01-cluster01-silver-nonrepl   |
| Silver        | repl        | c01-cluster02 | silver                                   | cluster02-silver-repl      | cloudstorage:cluster02-silver<br />StorageReplication:yes   | `<locationCode>`-vmfs-c01-cluster02-silver-repl      |
| Silver        | nonrepl     | c01-cluster02 | silver                                   | cluster02-silver-nonrepl   | cloudstorage:cluster02-silver<br />StorageReplication:no    | `<locationCode>`-vmfs-c01-cluster02-silver-nonrepl   |
|               |             | 🠋            |                                          |                            |                                                             |                                                      |
| Gold          | repl        | c01-cluster01 | gold                                     | cluster01-gold-repl        | cloudstorage:cluster01-gold<br />StorageReplication:yes     | `<locationCode>`-vmfs-c01-cluster01-gold-repl        |
| Gold          | nonrepl     | c01-cluster01 | gold                                     | cluster01-gold-nonrepl     | cloudstorage:cluster01-gold<br />StorageReplication:no      | `<locationCode>`-vmfs-c01-cluster01-gold-nonrepl     |
| Gold          | repl        | c01-cluster02 | gold                                     | cluster02-gold-repl        | cloudstorage:cluster02-gold<br />StorageReplication:yes     | `<locationCode>`-vmfs-c01-cluster02-gold-repl        |
| Gold          | nonrepl     | c01-cluster02 | gold                                     | cluster02-gold-nonrepl     | cloudstorage:cluster02-gold<br />StorageReplication:no      | `<locationCode>`-vmfs-c01-cluster02-gold-nonrepl     |
|               |             | 🠋            |                                          |                            |                                                             |                                                      |
| Platinum      | repl        | c01-cluster01 | platinum                                 | cluster01-platinum-repl    | cloudstorage:cluster01-platinum<br />StorageReplication:yes | `<locationCode>`-vmfs-c01-cluster01-platinum-repl    |
| Platinum      | nonrepl     | c01-cluster01 | platinum                                 | cluster01-platinum-nonrepl | cloudstorage:cluster01-platinum<br />StorageReplication:no  | `<locationCode>`-vmfs-c01-cluster01-platinum-nonrepl |
| Platinum      | repl        | c01-cluster02 | platinum                                 | cluster02-platinum-repl    | cloudstorage:cluster02-platinum<br />StorageReplication:yes | `<locationCode>`-vmfs-c01-cluster02-platinum-repl    |
| Platinum      | nonrepl     | c01-cluster02 | platinum                                 | cluster02-platinum-nonrepl | cloudstorage:cluster02-platinum <br />StorageReplication:no | `<locationCode>`-vmfs-c01-cluster02-platinum-nonrepl |
|               |             | 🠋            |                                          |                            |                                                             |                                                      |
| Diamond       | repl        | c01-cluster01 | diamond                                  | cluster01-diamond-repl     | cloudstorage:cluster01-diamond<br />StorageReplication:yes  | `<locationCode>`-vmfs-c01-cluster01-diamond-repl     |
| Diamond       | nonrepl     | c01-cluster01 | diamond                                  | cluster01-diamond-nonrepl  | cloudstorage:cluster01-diamond<br />StorageReplication:no   | `<locationCode>`-vmfs-c01-cluster01-diamond-nonrepl  |
| Diamond       | repl        | c01-cluster02 | diamond                                  | cluster02-diamond-repl     | cloudstorage:cluster02-diamond<br />StorageReplication:yes  | `<locationCode>`-vmfs-c01-cluster02-diamond-repl     |
| Diamond       | nonrepl     | c01-cluster02 | diamond                                  | cluster02-diamond-nonrepl  | cloudstorage:cluster02-diamond<br />StorageReplication:no   | `<locationCode>`-vmfs-c01-cluster02-diamond-nonrepl  |

## Monitoring Storage Class

vROPs has out of the box available alert "Virtual machine is violating vSphere Security Configuration Guide" which is used for this purpose. This alert is mapped to symptom definition "Virtual machine does not comply with to storage policy"

## Reporting of Storage Class

The  reports shared with "Global Cloud Services Metering and Charging (GCS-MC) integration via Cloud Service Infrastructure (CSI)",  must include total consumed storage per VM per Storage Class.

This is done by cycling to each disk per VM, and use following IOPS limit criteria to define storage class of VM.

If current disk IOPS Limit = 0 mark storage class as database.<br />
If current disk IOPS Limit > 0 && <= 100 mark storage class as bronze.<br />
If current disk IOPS Limit > 100 && <= 250 mark storage class as silver.<br />
If current disk IOPS Limit > 250 && <= 1000 mark storage class as gold.<br />
If current disk IOPS Limit > 1000 && <= 3000 mark storage class as platinum. <br />
If current disk IOPS Limit > 3000 && <= 6000 mark storage class as diamond.<br />

The above upper and lower limits per class are placed and read from the config file of the report generation scripts to allow easy modification in future.

## 2nd Day Operations on VMs

Natural progression of Storage Classes is to let user modify the Storage Class per Virtual Machine Disk.
Following are design decisions to proceed with that,
A vRA resource action is created.

Resource Action : Resource Action name is "Change Disk Storage Class".It is created with resource type as "Cloud.vSphere.Machine". This action allows end user to select the virtual Machine disk and change the storage class of this disk. This action is available on VM object within Deployment record. For os disk, user is able to select "silver" or above storage profile (i.e.storage profile with IOPs Limit >=250) as new storage profile which needs to be applied to os disk. Also the database profile (i.e. high iops storage profile with unlimited IOPs) can be assigned to OS disk.

On resource action custom form, user selects the disk from list of Virtual Machine disks attached to vm. The existing storage profile associated with selected disk is displayed on form. User selects the required storage profile from list of available and valid storage profiles. Upon submission of the resource action, it triggers custom vRO workflow to update the storage policy of disk in vcenter based on the selected vRA storage profile.

### VMFS on FC

In the environments where VMFS on FC is used the 2nd day action "Change Disk Storage Class" has slightly different behavior.
The logic which prevents user to select "slow" Storage Class for OS drive is removed. The reasoning behind it is that since IOPS limits are set on array level this information is not presented to vSphere environment. For each customer these limits can be different and the same storage class may have significantly different performance across different customers.

Apart from above once the Storage Profile is applied on VM disk the workflow will migrate (using VMware Storage vMotion mechanism) given disk to a vSphere Datastore Cluster that is compliant with selected Storage Profile. In effect, different VM disks can be located on different Datastores (and Datastore Clusters) matching their Storage Policy.

## Automation Guidelines

1. Rename "Storage_tag" in catalog to "Storage Class"
2. Use "Thin Provisioning" option in storage profile
3. Do not use datastore binding in storage profile
4. Do not set IOPS limits in storage profile.
