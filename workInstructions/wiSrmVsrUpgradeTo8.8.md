# Site Recovery Manager and vSphere Replication version 8.8 upgrade

# Table of Contents

- [Site Recovery Manager and vSphere Replication version 8.8 upgrade](#site-recovery-manager-and-vsphere-replication-version-88-upgrade)
- [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
- [Introduction](#introduction)
- [Related Documents](#related-documents)
- [SRM and vSR upgrade](#srm-and-vsr-upgrade)
  - [vSR appliance upgrade to version 8.8](#vsr-appliance-upgrade-to-version-88)
  - [SRM appliance upgrade process to version 8.8](#srm-appliance-upgrade-process-to-version-88)
- [Aviva upgrade](#aviva-upgrade)

# Changelog

| Date       | Issue     | Author          | TOS | Description                                                                                             |
|------------|-----------|-----------------|-----|---------------------------------------------------------------------------------------------------------|
| 25.06.2024 | VCS-13153 | Mariusz Stanek |     | Initial version |

# Introduction

Document describes upgrade to Site Recovery Manger 8.8 from version 8.7.

# Related Documents

| Document                                                                 |
|--------------------------------------------------------------------------|
| [LLD Disaster Recovery](../design/lldDisasterRecovery.md)                |
| [LLD Software Defined Networks](../design/lldSoftwareDefinedNetworks.md) |
| [WI A/P Integration](wiIntegrateActivePassiveDr.md)                      |
| [WI A/P Failover](wiFailoverActivePassiveDr.md)                          |
| [WI Disaster Recovery SDN](wiDisasterRecoverySdn.md)                     |

# SRM and vSR upgrade

## vSR appliance upgrade to version 8.8

`VCS uses vSphere Replication for VSAN only, hence skip this chapter if array-based principal storage is used.`

Read [Upgrading vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/8.8/administration-guide/GUID-30083484-FB13-485E-AEC9-0695EADB7B3D.html) documentation to understand upgrade scenarios, order and more.

Minor steps are:

1. Download [VMware-vSphere_Replication-8.8.x.x-build_number.iso](https://support.broadcom.com/group/ecx/productfiles?displayGroup=VMware%20vSphere%20-%20Essentials%20Plus&release=7.0&os=&servicePk=202623&language=EN&groupId=204416) image and plugin from vSphere Downloads page. Just make sure you are going to use the same version for vSR and SRM. Test were done on 8.8.0.3 but advice is to use the latest build number with recent patches.
2. [Upgrade vSphere Replication Appliance](https://docs.vmware.com/en/vSphere-Replication/8.8/administration-guide/GUID-B04CAEF2-FDE0-44CC-81F1-4ABFF731C715.html)

    >Note: Remember to unmap cd-rom from *vsr001* VM after an upgrade.
3. [Update a VMware Aria Automation Orchestrator Plug-In for vSphere Replication 8.8](https://docs.vmware.com/en/vSphere-Replication/8.8/plugin-guide/GUID-077EE17C-6064-4A2C-88C1-F38F97EB7121.html).

## SRM appliance upgrade process to version 8.8

Make sure you get familiar with [Prerequisites and Best Practices for Site Recovery Manager Upgrade.](https://docs.vmware.com/en/Site-Recovery-Manager/8.8/srm-installation-and-configuration/GUID-C9BD3EEA-73F3-4784-8D7A-EB281C69C4D0.html)

Notice an [Order of Upgrading vSphere and Site Recovery Manager Components](https://docs.vmware.com/en/Site-Recovery-Manager/8.8/srm-installation-and-configuration/GUID-E7B47738-C63D-4A05-9A13-7C5FF20801A7.html).

**vSR must go before SRM**.

Minor steps are:

1. [Download VMware Site Recovery Manager package and plugin](https://support.broadcom.com/group/ecx/productfiles?subFamily=VMware%20Site%20Recovery%20Manager&displayGroup=VMware%20Site%20Recovery%20Manager&release=8.8.0.3&os=&servicePk=203091&language=EN). Just make sure you are going to use the same version for vSR and SRM. Test were done on 8.8.0.3, but advice is to use the latest build number with recent patches.
2. Upgrade to version 8.8 - [Update the Site Recovery Manager Virtual Appliance](https://docs.vmware.com/en/Site-Recovery-Manager/8.8/srm-installation-and-configuration/GUID-8C1A5F59-EFA6-492F-A28A-5EFE2EC0B180.html)
   >Note: Remember to unmap cd-rom from *srm001* VM after an upgrade.
3. [Update a VMware Aria Automation Orchestrator Plug-In for Site Recovery Manager 8.8](https://docs.vmware.com/en/Site-Recovery-Manager/8.8/orchestrator-plugin-srm/GUID-46DE78C1-0C5D-4FAE-A308-B4E5BB7721E9.html).

# Aviva upgrade

Moved to separate [document](wiDrCustomizationForAviva.md)
