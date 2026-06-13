# VSAN Stretched Cluster Startup Order Test

# Changelog
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 10/01/2020 | First version | Maciej Losek |
| 0.2     | 25/09/2020 | cht rename to bil | Tomasz Jasionowski |

## Introduction

### Purpose

Test correct startup order of all Mgmt vms on DR site to make sure that all dependencies and startup priorities are met.

### Audience

- VCS Operations
- VCS Engineers

### Scope

- Validate restart priority
- Power off ESXi hosts
- Observe VMs start up on secondary site
- Validate service continuity

## Scenario

Steps that should be taken to test correct startup order of all Mgmt vms on DR site:

1. Check that all mgmt vms have defined "VM restart priority" according to lldInfrastructure.md. "VM restart priority" determines the relative order in which virtual machines are placed on new hosts after a host failure. Such virtual machines are restarted, with the highest priority virtual machines attempted first and continuing to those with lower priority until all virtual machines are restarted or no more cluster resources are available.

   ```yaml
   vmObjects:
   HIGHEST
     - vmName: "< mgmtDns.adc001.name >"
       priority: Highest
     - vmName: "< mgmtDns.adc002.name >"
       priority: Highest
     - vmName: "< mgmtDns.ctl011.name >"
       priority: Highest
     - vmName: "< mgmtDns.ctl012.name >"
       priority: Highest
     - vmName: "< mgmtDns.ctl013.name >"
       priority: Highest
   HIGH
     - vmName: "< mgmtDns.psc001.name >"
       priority: High
     - vmName: "< mgmtDns.psc002.name >"
       priority: High
     - vmName: "< mgmtDns.pxy002.name >"
       priority: High
     - vmName: "< mgmtDns.cas001.name >"
       priority: High
     - vmName: "< mgmtDns.kms001.name >"
       priority: High
     - vmName: "< mgmtDns.idm001.name >"
       priority: High
     - vmName: "< mgmtDns.tss001.name >"
       priority: High
     - vmName: "< mgmtDns.ans001.name >"
       priority: High    
   MEDIUM
     - vmName: "< mgmtDns.edg001.name >"
       priority: Medium
     - vmName: "< mgmtDns.vcs001.name >"
       priority: Medium
     - vmName: "< mgmtDns.vcs002.name >"
       priority: Medium
     - vmName: "< mgmtDns.hsv001.name >"
       priority: Medium
     - vmName: "< mgmtDns.ops002.name >"
       priority: Medium
     - vmName: "< mgmtDns.cas001.name >"
       priority: Medium
     - vmName: "< mgmtDns.inf002.name >"
       priority: Medium
     - vmName: "< mgmtDns.vrli01a.name >"
       priority: Medium
     - vmName: "< mgmtDns.vrli01b.name >"
       priority: Medium
     - vmName: "< mgmtDns.vrli01c.name >"
       priority: Medium
     - vmName: "< mgmtDns.mid001.name >"
       priority: Medium
     - vmName: "< mgmtDns.mid002.name >"
       priority: Medium
     - vmName: "< mgmtDns.mid003.name >"
       priority: Medium
   LOW
     - vmName: "< mgmtDns.nsx001.name >"
       priority: Low
     - vmName: "< mgmtDns.edg002.name >"
       priority: Low
     - vmName: "< mgmtDns.pxy003.name >"
       priority: Low
     - vmName: "< mgmtDns.ops003.name >"
       priority: Low
     - vmName: "< mgmtDns.sdm001.name >"
       priority: Low
     - vmName: "< mgmtDns.lcm001.name >"
       priority: Low
     - vmName: "< mgmtDns.ave001.name >"
       priority: Low
     - vmName: "< mgmtDns.avp001.name >"
       priority: Low
     - vmName: "< mgmtDns.wus001.name >"
       priority: Low
     - vmName: "< mgmtDns.deb001.name >"
       priority: Low
     - vmName: "< mgmtDns.kms002.name >"
       priority: Low
     - vmName: "< mgmtDns.tss002.name >"
       priority: Low
     - vmName: "<  mgmtDns.ica001.name >"
       priority: Low
     - vmName: "< mgmtDns.rca001.name >"
       priority: Low
     - vmName: "< mgmtDns.inf003.name >"
       priority: Low
     - vmName: "< mgmtDns.awx001.name >"
       priority: Low
     - vmName: "< mgmtDns.apt001.name >"
       priority: Low
     - vmName: "< mgmtDns.bil001.name >"
       priority: Low
     - vmName: "< mgmtDns.hgw001.name >"
       priority: Low
   ```

2. Using vSphere Client make sure that all AZ1 vms are hosted by ESXi hosts in primary site.
3. Make sure that vSAN Stretched Cluster is Healthy
4. Start pinging all mgmt vms using fping tool.
5. Log in to all AZ1 ESXi hosts via iDRAC.
6. Using "Power Control" option execute a "Power Off System" action for all AZ1 ESXi hosts.
7. During tests vCenter server will be unavailable. VC vm should be started in a 4th priority group (High).
8. Using fping, monitor if vms are restarted on Secondary Site in order specified in VM restart priority setting.  
VMs that have defined "Highest" priority must be pingable before any other vms."High" priority vms must be restarted before "Medium", "Low" and "Lowest" priority vms and so on.
When VCSA is up, log in and check if all vms are vMotioned successfully from AZ1 to AZ2.
9. When all vMotion tasks are done all vms should be hosted by ESXi hosts in AZ2 and all vms are pingable.
10. Check if all mgmt services work correctly:
    - Domain Controllers and DNS;
    - log in to both VCSA (mgmt and cmp) using `administrator@vsphere.local` account and check vm status;
    - log in to TSS01 and TSS02 using domain account;
    - check ssh connectivity to ans001
    - check if <https://vIDM_FQDN> is responsive, log in and check if all AD groups and users are synced.
    - check if <https://Vault_FQDN> is responsive
    - check if <https://vROPS_FQDN> is responsive, log in and check stretched cluster dashboard- both fault domain, hosts and vms should be visible
    - check if <https://vRLI_FQDN> is responsive and log in
    - check ssh connectivity to all MID servers
    - check vRA Cloud vm deployment
    - check if <https://SDDCMANAGER_FQDN> is responsive and log in, check AZ1 MGMT ESXi hosts status

# Result of tests

Tests of correct startup order on DR site for Mgmt Domain were performed in NX5 environment and finished successfully. Due to fact that this env was not integrated to vRA Cloud, Service Now and KMS these services couldn't be tested.
Rest of services were responsive. Below link to recorded presentation and logs as a proof:

#### Step 1- all mgmt vms had defined "VM restart priority" according to lldInfrastructure.md

![Figure 1](images/dhcVsanStretchedCluster_DRtest_startupOrder/1.jpg)
![Figure 2](images/dhcVsanStretchedCluster_DRtest_startupOrder/2.jpg)
![Figure 3](images/dhcVsanStretchedCluster_DRtest_startupOrder/3.jpg)
![Figure 4](images/dhcVsanStretchedCluster_DRtest_startupOrder/4.jpg)

#### Step 2- all vms run on ESXi hosts in Primary Zone- vms were up and pingable running

![Figure 4](images/dhcVsanStretchedCluster_DRtest_startupOrder/5.jpg)
![Figure 4](images/dhcVsanStretchedCluster_DRtest_startupOrder/5_1.jpg)
![Figure 4](images/dhcVsanStretchedCluster_DRtest_startupOrder/5_2.jpg)
![Figure 4](images/dhcVsanStretchedCluster_DRtest_startupOrder/5_3.jpg)

#### Step 3- vSAN Stretched Cluster were Healthy

![Figure 5](images/dhcVsanStretchedCluster_DRtest_startupOrder/6.jpg)

#### Step 4 - Ping started

![Figure 6](images/dhcVsanStretchedCluster_DRtest_startupOrder/7.jpg)

#### Step 5,6- AZ1 ESXi hosts shutted down

![Figure 7](images/dhcVsanStretchedCluster_DRtest_startupOrder/8.jpg)

#### Step 7, 8- All vms become unavailable

![Figure 8](images/dhcVsanStretchedCluster_DRtest_startupOrder/9.jpg)
![Figure 9](images/dhcVsanStretchedCluster_DRtest_startupOrder/9_1.jpg)
  
   VMs restart in order:
![Figure 10](images/dhcVsanStretchedCluster_DRtest_startupOrder/10.jpg)
![Figure 11](images/dhcVsanStretchedCluster_DRtest_startupOrder/10_1.jpg)
![Figure 12](images/dhcVsanStretchedCluster_DRtest_startupOrder/10_2.jpg)
![Figure 13](images/dhcVsanStretchedCluster_DRtest_startupOrder/10_3.jpg)
![Figure 14](images/dhcVsanStretchedCluster_DRtest_startupOrder/10_4.jpg)
![Figure 15](images/dhcVsanStretchedCluster_DRtest_startupOrder/10_5.jpg)
![Figure 16](images/dhcVsanStretchedCluster_DRtest_startupOrder/10_6.jpg)
![Figure 17](images/dhcVsanStretchedCluster_DRtest_startupOrder/10_7.jpg)
![Figure 18](images/dhcVsanStretchedCluster_DRtest_startupOrder/10_8.jpg)
![Figure 19](images/dhcVsanStretchedCluster_DRtest_startupOrder/10_9.jpg)
![Figure 21](images/dhcVsanStretchedCluster_DRtest_startupOrder/10_10.jpg)

Cluster HA logs (fdm.log):
![Figure 22](images/dhcVsanStretchedCluster_DRtest_startupOrder/20.jpg)
![Figure 23](images/dhcVsanStretchedCluster_DRtest_startupOrder/21.jpg)
![Figure 24](images/dhcVsanStretchedCluster_DRtest_startupOrder/22.jpg)
![Figure 25](images/dhcVsanStretchedCluster_DRtest_startupOrder/23.jpg)
![Figure 26](images/dhcVsanStretchedCluster_DRtest_startupOrder/24.jpg)
![Figure 27](images/dhcVsanStretchedCluster_DRtest_startupOrder/25.jpg)

#### Step 9- All vms have been vmotioned to AZ2

![Figure 28](images/dhcVsanStretchedCluster_DRtest_startupOrder/11.jpg)
![Figure 29](images/dhcVsanStretchedCluster_DRtest_startupOrder/11_1.jpg)

#### Step 10- Checking if all services are running

Due to fact that some components were not configured in test env (nx5) i.e: KMS, CAS proxy, edge101-1, MID servers, checking was limited to:

- Active Directory and DNS services - done;
- NSXV and NSXT - done;
- vCenter servers (mgmt and cmp) - done;
- Terminal servers - done;
- ssh to ans001 - done;
- log in to <https://vIDM_FQDN> - done;
- log in to <https://vRLI> - done;
- log in to <https://vROPS_FQDN> - done;
- log in to <https://SDDCManager_FQDN> - done;
