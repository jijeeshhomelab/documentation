# VSAN Stretched Clouster vMotion Tests

# Changelog
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 10/01/2020 | First version | Maciej Losek |
| 0.2     | 21/06/2022 | Minor updates | Lukasz Tomaszewski |

## Introduction

### Purpose

Test vMotion between two Availability Zones in vSAN Stretched Cluster in Management Domain and Workload Domain.

### Audience

- VCS Operations
- VCS Engineers

### Scope

1. migration of all management VMs into secondary Availability Zone in vSAN Stretched Cluster (using DRS Rules);
2. migration of single management VM into AZ2 (manually).
3. migration of all workload VMs into secondary Availability Zone in vSAN Stretched Cluster (using DRS Rules);
4. migration of single workload VM into secondary Availability Zone in vSAN Stretched Cluster.

## Scenario 1 vMotion of all VMs to AZ2

Steps that should be taken to perform planned vMotion of all management VMs into secondary Availability Zone and all workload VMs into secondary Availability Zone(these tests will be executed using DRS Rules).

1. The entry point is that all VMs run on ESXi hosts in Primary Zone and all VMs are pingable.
2. If doesn't exist, using vSphere Client create new VM Group "< locationCode >-< type >01-drs-vmgroup01". All VMs that should run in AZ1 have to be members of that group. NOTE: DRS group name must be in line with namingConvention.md design document.
3. If doesn't exist, create new Host Group "< locationCode >-< type >01-hostgroup01". All ESXi hosts from Availability Zone 2 have to be members of that group. NOTE: DRS group name must be in line with namingConvention.md design document.
4. Start pinging all mgmt VMs using i.e fping tool. This binary can be installed on Ubuntu VMs by executing: sudo apt install fping.
5. If doesn't exist, create DRS Rule named "< locationCode >-< type >01-drs-vmhostrule01":  Group "< locationCode >-< type >01-drs-vmgroup01" must run on hosts that are members of the "< locationCode >-< type >01-hostgroup01". NOTE: DRS group name must be in line with namingConvention.md design document.
6. vMotion processes should start automatically.
7. Monitor whether all VMs are vMotion from AZ1 to AZ2.
8. When all vMotion tasks are done all VMs should be hosted by ESXi hosts in AZ2 and all of them are still pingable.

## Scenario 2 vMotion of single VM to AZ2

Steps that should be taken to perform planned manually vMotion of single management VM ( i.e. Active Directory Domain Controller 001) into secondary Availability Zone and vMotion of single workload VM into secondary Availability Zone.

1. The entry point is that VM is running on ESXi host in Primary Zone and is pingable.
2. Start pinging single VM.
3. Using vSphere Client migrate (changed compute resource only) to ESXi host in secondary zone.
4. "Relocate virtual machine" task should finished successful and VM is still pingable.

# Tests result

All tests (Scenario 1 and Scenario 2) for Mgmt Domain and Workload Domain were performed one by one and all finished successfully. Below screenshots as a proof:

## Scenario 1 test result - vMotion of all Mgmt VMs to AZ2

#### Step 1- all VMs run on ESXi hosts in Primary Zone - VMs are up and running

![Figure 1](images/dhcVsanStretchedCluster_PlannedVmotionTests/1.JPG)
![Figure 2](images/dhcVsanStretchedCluster_PlannedVmotionTests/2.JPG)
![Figure 3](images/dhcVsanStretchedCluster_PlannedVmotionTests/3.JPG)

#### Step 2- New VM Group "< locationCode >-< type >01-drs-vmgroup01" was created

![Figure 4](images/dhcVsanStretchedCluster_PlannedVmotionTests/4.JPG)

#### Step 3- new Host Group "< locationCode >-< type >01-hostgroup01" was created

![Figure 5](images/dhcVsanStretchedCluster_PlannedVmotionTests/5.JPG)

#### Step 4- Ping started

![Figure 6](images/dhcVsanStretchedCluster_PlannedVmotionTests/12_3.jpg)

#### Step 5- DRS Rule named "< locationCode >-< type >01-drs-vmhostrule01" was created

![Figure 7](images/dhcVsanStretchedCluster_PlannedVmotionTests/6.JPG)

#### Step 6- vMotion proccesses started automaticaly

![Figure 8](images/dhcVsanStretchedCluster_PlannedVmotionTests/7.JPG)
![Figure 9](images/dhcVsanStretchedCluster_PlannedVmotionTests/8.JPG)

#### Step 7, 8- All VMs were vMotioned from AZ1 to AZ2 successfully

![Figure 10](images/dhcVsanStretchedCluster_PlannedVmotionTests/9.JPG)
![Figure 11](images/dhcVsanStretchedCluster_PlannedVmotionTests/10.JPG)
![Figure 12](images/dhcVsanStretchedCluster_PlannedVmotionTests/11.JPG)
![Figure 13](images/dhcVsanStretchedCluster_PlannedVmotionTests/12_2.jpg)

## Scenario 2 test result - vMotion of single Mgmt VM to AZ2

#### Step 1- single VM (adc001) run on ESXi hosts in Primary Zone - VM is running and pingable

![Figure 13](images/dhcVsanStretchedCluster_PlannedVmotionTests/13.jpg)

#### Step 2- Manually migration of ADC001 VM to AZ2

![Figure 15](images/dhcVsanStretchedCluster_PlannedVmotionTests/15.jpg)
![Figure 15](images/dhcVsanStretchedCluster_PlannedVmotionTests/16.jpg)

#### Step 3, 4- VM migrated successfully and ADC001 was still pingable

![Figure 16](images/dhcVsanStretchedCluster_PlannedVmotionTests/17.jpg)
![Figure 17](images/dhcVsanStretchedCluster_PlannedVmotionTests/18.jpg)

## Scenario 1 test result - vMotion of all Cmp VMs to AZ2

#### Step 1- all VMs run on ESXi hosts in Primary Zone - VMs are up and running

![Figure 18](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp1.jpg)
![Figure 19](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp2.jpg)

#### Step 2- New VM Group "< cmp_cluster_name >_ primary-az-vmgroup" was created

![Figure 20](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp3.jpg)

#### Step 3- new Host Group "< cmp_cluster_name >_ secondary-az-hostgroup" was created

![Figure 21](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp4.jpg)

#### Step 4- Ping started

![Figure 22](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp5.jpg)

#### Step 5- DRS Rule named "< cmp_cluster_name >_VMs should be on Secondary Site" was created

![Figure 23](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp4_1.jpg)

#### Step 6- vMotion processes started automatically

![Figure 24](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp7_1.jpg)

#### Step 7, 8- All VMs were vMotioned from AZ1 to AZ2 successfully

![Figure 25](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp6.jpg)
![Figure 26](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp7.jpg)

## Scenario 2 test result - vMotion of single Cmp VM to AZ2

#### Step 1- single VM (EDG004) run on ESXi hosts in Primary Zone - VM is running and pingable

![Figure 27](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp8.jpg)

#### Step 2- Manually migration of EDG004 VM to AZ2

![Figure 28](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp12.jpg)
![Figure 29](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp13.jpg)

#### Step 3, 4- VM migrated successfully and EDG004 was still pingable

![Figure 30](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp9.jpg)
![Figure 30](images/dhcVsanStretchedCluster_PlannedVmotionTests/cmp11.jpg)
