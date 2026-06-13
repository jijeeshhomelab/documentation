# Site Recovery Manager and vSphere Replication upgrade

# Table of Contents

- [Site Recovery Manager and vSphere Replication upgrade](#site-recovery-manager-and-vsphere-replication-upgrade)
- [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
- [Introduction](#introduction)
- [Related Documents](#related-documents)
- [SRM and vSR upgrade](#srm-and-vsr-upgrade)
  - [vSR appliance upgrade to version 8.7](#vsr-appliance-upgrade-to-version-87)
  - [SRM appliance upgrade process to version 8.7](#srm-appliance-upgrade-process-to-version-87)
- [Aviva upgrade](#aviva-upgrade)

# Changelog

| Date       | Issue     | Author          | TOS | Description                                                                                             |
|------------|-----------|-----------------|-----|---------------------------------------------------------------------------------------------------------|
| 19.07.2023 |           | Robert Kaminski |     | Initial draft version for array based compute storage, AVIVA upgrade recommendations                    |
| 06.09.2023 | DHC-10600 | Robert Kaminski |     | Added vSR upgrade for VSAN compute storage, excluded AVIVA upgrade recommendations to separate document |

# Introduction

Site Recovery Manager 8.5.x is the last general version to support storage policy protection groups (SPPG). Before upgrading to Site Recovery Manger 8.7, all storage policy protection groups must be migrated to regular array-based replication protection groups.

# Related Documents

| Document                                                                 |
|--------------------------------------------------------------------------|
| [LLD Disaster Recovery](../design/lldDisasterRecovery.md)                |
| [LLD Software Defined Networks](../design/lldSoftwareDefinedNetworks.md) |
| [WI A/P Integration](wiIntegrateActivePassiveDr.md)                      |
| [WI A/P Failover](wiFailoverActivePassiveDr.md)                          |
| [WI Disaster Recovery SDN](wiDisasterRecoverySdn.md)                     |

# SRM and vSR upgrade

## vSR appliance upgrade to version 8.7

`VCS uses vSphere Replication for VSAN only, hence skip this chapter if array-based principal storage is used.`

Read [Upgrading vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/8.7/com.vmware.vsphere.replication-admin.doc/GUID-30083484-FB13-485E-AEC9-0695EADB7B3D.html) documentation to understand upgrade scenarios, order and more.

Minor steps are:

1. Download [VMware-vSphere_Replication-8.7.x.x-build_number.iso](https://customerconnect.vmware.com/downloads/details?downloadGroup=VR8702&productId=974#product_downloads) image and plugin from vSphere Downloads page. Just make sure you are going to use the same version for vSR and SRM. Test were done on v8.7.0.2 but advice is to use the latest build number with recent patches.
2. [Upgrade vSphere Replication Appliance](https://docs.vmware.com/en/vSphere-Replication/8.7/com.vmware.vsphere.replication-admin.doc/GUID-B04CAEF2-FDE0-44CC-81F1-4ABFF731C715.html)

    >Note: Remember to unmap cd-rom from *vsr001* VM after an upgrade.
3. [Update a vRealize Orchestrator Plug-In](https://docs.vmware.com/en/vRealize-Orchestrator/8.11/com.vmware.vrealize.orchestrator-install-config.doc/GUID-A71B3938-4549-4F12-9638-7A1BF50FE48F.html) for vSphere Replication.

## SRM appliance upgrade process to version 8.7

Make sure you get familiar with [Prerequisites and Best Practices for Site Recovery Manager Upgrade.](https://docs.vmware.com/en/Site-Recovery-Manager/8.7/com.vmware.srm.install_config.doc/GUID-C9BD3EEA-73F3-4784-8D7A-EB281C69C4D0.html)

Notice an [Order of Upgrading vSphere and Site Recovery Manager Components](https://docs.vmware.com/en/Site-Recovery-Manager/8.7/com.vmware.srm.install_config.doc/GUID-E7B47738-C63D-4A05-9A13-7C5FF20801A7.html).

**vSR must go before SRM**.

Minor steps are:

1. [Download VMware Site Recovery Manager package and plugin](https://customerconnect.vmware.com/en/downloads/info/slug/infrastructure_operations_management/vmware_site_recovery_manager/8_7). Just make sure you are going to use the same version for vSR and SRM. Test were done on v8.7.0.2, but advice is to use the latest build number with recent patches.
2. (`SKIP for VSAN`) [Update the Site Recovery Manager Virtual Appliance](https://docs.vmware.com/en/Site-Recovery-Manager/8.7/com.vmware.srm.install_config.doc/GUID-8C1A5F59-EFA6-492F-A28A-5EFE2EC0B180.html) - Site Recovery Manager 8.5.x is the last general version to support storage policy protection groups (SPPG). In case SPPG are implemented **`before we jump to version 8.7, we need SRM 8.5.0.5 first, if we want to use migration tool for SPPG.`** Otherwise policy migration (step 3) can be skipped and upgrade directly to 8.7 - step 4.
3. (`SKIP for VSAN`) [Migrate your Storage Policy Protection Groups to Array-Based Replication Protection Groups.](https://docs.vmware.com/en/Site-Recovery-Manager/8.7/com.vmware.srm.install_config.doc/GUID-5AEF68E2-1E1C-4313-8C48-B88329E67633.html)

    [Syntax of the Storage Policy Protection Groups Migration Tool](https://docs.vmware.com/en/Site-Recovery-Manager/8.7/com.vmware.srm.install_config.doc/GUID-6633D4C7-22A0-4437-8502-980BCCC0F871.html)
4. Upgrade to version 8.7 - [Update the Site Recovery Manager Virtual Appliance](https://docs.vmware.com/en/Site-Recovery-Manager/8.7/com.vmware.srm.install_config.doc/GUID-8C1A5F59-EFA6-492F-A28A-5EFE2EC0B180.html)
   >Note: Remember to unmap cd-rom from *srm001* VM after an upgrade.
5. [Update a vRealize Orchestrator Plug-In](https://docs.vmware.com/en/vRealize-Orchestrator/8.11/com.vmware.vrealize.orchestrator-install-config.doc/GUID-A71B3938-4549-4F12-9638-7A1BF50FE48F.html) for Site Recovery Manager.

Note. There were issues noticed while upgrading to 8.5.0.5 on the dev environments, fix in the links below:

- [Unable to reconfigure SRM Appliance after upgrade - Host could not be contacted (83185)](https://kb.vmware.com/s/article/83185)

- [How to remove SRM extension from vCenter Server](https://defaultreasoning.com/2016/03/15/how-to-remove-srm-extension-from-vcenter-server/)

# Aviva upgrade

Moved to separate [document](wiDrCustomizationForAviva.md)
