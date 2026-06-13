# vSAN Stretched Cluster Failover

# Changelog
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 20/02/2020 | First version | Maciej Losek |
| 0.2     | 28/08/2020 | 02v - sentence to force resync vsan obcjects added | Maciej Losek |

# Related documents
  
| Document Name |
| ------- |
| [lldInfrastructure](../design/lldInfrastructure.md) |

## Introduction

### Purpose

Enable failover on vSAN Stretched Cluster of the Management Domain and Workload Domain at the same time.

### Audience

- VCS Engineers
- VCS Operations

### Scope

The vSAN Stretched Cluster is utilized to manage the failover of servers from Availability Zone 1, which is the Preferred Fault Domain, to Availability Zone 2, the Secondary Fault Domain. The process involves intentionally disconnecting the network for Availability Zones 1 in both the Mgmt and Cmp clusters. Subsequently, the High Availability (HA) mechanism will be triggered, resulting in the powering off and then powering on of all virtual machines (VMs) in Availability Zone 2.

The purpose of this scenario is to perform a Disaster Recovery (DR) test to simulate an unplanned network disconnection affecting the entire rack, including both the Mgmt cluster and Cmp cluster. The aim of the test is to verify whether all Mgmt and Cmp VMs can successfully move to the secondary zone during this disruption.

# DR Pre-checks

## vSAN Pre-checks

1. Select the Cluster object in the vCenter inventory, click on Monitor > vSAN > Health. Ensure the Stretched Cluster Health Checks Object Health are marked on green.
2. Select the Cluster object in the vCenter inventory, click on VMs on the right side of window and make sure that all vms that should run on AZ1 are on the proper site (AZ1). All vms are pingable.

# Performing DR Failover

During failover test all vms (in Mgmt and Cmp clusters) are powered off and powered on on the Availability Zone 2 (secondary Fault Domain). In this scenario Preferred Fault Domain is disconnected.

## Steps that should be taken to test unplanned network disconnection

1. Start pinging all vms using fping tool.
2. On both Top of Rack Switches (ToR X and ToR Y - names depend on environments) on trunk interface port channel ZZ (rack uplink- depends on environment) remove vlans xx-xy (vland tag depends on environment.)
3. During tests vCenter server will be unavailable.
4. Using fping, monitor if vms from mgmt and cmp cluster are restarted on Secondary Site.
5. VMs should restart in order specified under restart priority settings.
6. When VCSA is up, log in and check if all vms from mgmt and cmp clusters are vMotioned successfully from AZ1 to AZ2.
7. Once vms are started on secondary zone, all of them are pingable.

# Post Failover testing

1. Check if all mgmt services work correctly:
   - Domain Controllers and DNS;
   - log in to both VCSA (mgmt and cmp) using `administrator@vsphere.local` account ( check vms status );
   - log in to TSS01 and TSS02 using domain account;
   - check ssh connectivity to ans001;
   - check if <https://vIDM_FQDN> is responsive, log in and check if all AD groups and users are synced.
   - check if <https://Vault_FQDN> is responsive
   - check if <https://vROPS_FQDN> is responsive, log in and check stretched cluster dashboard- both fault domain, hosts and vms should be visible;
   - check if <https://vRLI_FQDN> is responsive and log in
   - check if <https://NSXV_Manager_FQDN> is responsive and log in;
   - check if <https://NSXT_Manager_FQDN> is responsive and log in - check edge nodes status;
   - check ssh connectivity to all MID servers
   - check vRA Cloud vm deployment
   - check if <https://SDDCMANAGER_FQDN> is responsive and log in, check AZ1 MGMT ESXi hosts status
2. Check if CMP vms works properly.
3. Check vSAN Health status of both clusters. It may happen that some vsan components and objects will be in Absent state. It means that vSAN detects a temporary component failure where the component might recover and restore its working state. vSAN should start rebuilding components if that are not available within a certain time interval. By default in VCS, vSAN starts rebuilding absent components after 60 minutes. To speed up the objects resyncing process click on cluster_name -> Monitor -> Resync Objects -> expand Scheduled resyncing and click `RESYNC NOW`.

# Failback to Primary Site

Once primary site which has faced disaster by some means is functional as earlier, we can bring back vms (that should run on AZ1) from AZ2 to AZ1.

1. Select the Cluster object in the vCenter inventory, click on Configure > Configuration > VM/Host Rules.
2. Edit rule called {locationCode-WorkloadDomain-drs-vmhostrule01} (i.e. mec09-c01-drs-vmhostrule01) and change option 'Should run on hosts in group' to 'Must run on hosts in group'.
VMs migration will start automatically. When all vms are migrate back to AZ1 change back that are part of DRS rule option from 'Must run on hosts in group' to 'Should run on hosts in group'.
Ensure the Stretched Cluster Health Checks Object Health are marked on green.
