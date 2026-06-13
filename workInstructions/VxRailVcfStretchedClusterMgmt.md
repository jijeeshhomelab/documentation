# VxRail VCF Stretch Cluster Management

- Table of Contents
{:toc}

# Changelog
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 14/04/2020 | First version | Maciej Losek|

# Introduction

## Purpose

The purpose of this document is to describe steps that should be performed to stretch vSAN Mgmt cluster (management domain) in VCF on VxRail. The vSAN stretched cluster extends the cluster from one data site to two sites for high availability and load balancing.

## Scope

The scope of this document covers the following:

1. Stretch A Cluster for NSX-V (management domain) in VMware Cloud Foundation.

# VxRail nodes reset procedure

If not already done, reset VxRail nodes to factory settings (reimaging) that will be used to expand the cluster. This procedure is described in chapter 'VxRail 4.7 Nodes' of DPC.Next ESXi Refreshing procedure document <br><https://msdevopsconfluence.fsc.atos-services.net/pages/viewpage.action?spaceKey=DPC&title=DPC.Next+ESXi+refreshing><br>

# Stretch A Cluster for NSX-V (Management domain) in VMware Cloud Foundation 3.9 on Dell EMC VxRail

The vSAN stretched cluster extends the cluster from one data site to two sites for high availability and load balancing. You can stretch the cluster on the management domain using the Supportability and Serviceability Utility (SoS)

Steps to stretch a Management cluster are described in chapter 'Stretch A Cluster for NSX-V in VMware Cloud Foundation
3.9' of [VMware Cloud Foundation on Dell EMC VxRail Admin Guide](vcfOnVxrailAdministering.pdf) document.

To stretch a cluster for VMware Cloud Foundation on Dell EMC VxRail, perform the steps in following order:

1. Execute steps 1, 2 and 3 described in [vcfOnVxrailAdministering](vcfOnVxrailAdministering.pdf) chapter 'Stretch a Cluster for NSX-V in VMware Cloud Foundation 3.9'.
   Step 3 (cluster expansion) should be performed according to step 2 described in chapter 'Expand the VxRail Cluster' [vcfOnVxrailAdministering](vcfOnVxrailAdministering.pdf)

   >**NOTE!!!** DNS records for all of the hosts used to expand the cluster needs created before proceeding with cluster expansion.<br>
   >**NOTE!!!** For the ESXi Management Username same account as for existing management cluster needs to be used. This account should be already added to    Vault during management cluster creation for each existing ESXi MGMT hosts. Follow steps below to add root and management accounts entries for new hosts:
   - Login to <https://HashiVaultFQDN:8200> ;
   - Go to Secrets -> secret -> <customerCode> -> <locationCode> -> servers;
   - First create folder and add accounts related to new Host. Click 'Create secret +';
   - In the 'Path for this secret' add at the end of line Management hostname, i.e: mec09mgt005;
   - Under 'Version data' section type 'root' username as a key, and type password in the 'value' field. Click 'Add';
   - Type 'management' username as a key, and type password in the 'value' field. Click 'Save';

2. Deploy of witness appliance - details described in [dhcVsanWitnessAppliance.md](dhcVsanWitnessAppliance.md) document.
   Witness appliance should be deployed and initially configured by customer base on provided documentation.

3. Role dhc-configureVsanWitness has to be executed to add created witness appliance to the vCenter server.
   To execute the dhc-configureVsanWitness role run the playbook on ansible core VM with following command  `ansible-playbook configureVsanWitness.yml`.

4. Before created cluster can be stretched, VMkernel network adapters on all the hosts in the cluster needs to support witness traffic. To enable witness traffic on vmk02, SSH to each of the host in a cluster and run below command:
<br>`esxcli vsan network ip add -i vmk2 -T=witness`<br>

5. Execute step 4d from [vcfOnVxrailAdministering](vcfOnVxrailAdministering.pdf)
chapter 'Stretch a Cluster for NSX-V in VMware Cloud Foundation 3.9' and set Admission Control, the Host failures cluster tolerates to the number of hosts in fault domain 1.

6. To execute step 5 described in the same chapter of vcfOnVxrailAdministering.pdf logon to the sddc manager as vcf user next run the `su` command and provide the root credentials. Execute the sos command as described in vcfOnVxrailAdministering.pdf.  Use below command as example and replace values with the one from your environment:

   ```config
   /opt/vmware/sddc-support/sos --stretch-vsan --sc-domain (management doamin name e.g. MGMT) --sc-cluster (management cluster name e.g. gre07-m01-cluster01) --sc-hosts ( host names from fault domain 2 e.g. gre7mgt005.nx8dhc01.next,gre7mgt006.nx8dhc01.next,gre7mgt007.nx8dhc01.next,gre7mgt008.nx8dhc01.next) --witness-host-fqdn (witness fqdn e.g. gre03vwa001.nx8dhc01.next) --witness-vsanip (vSAN Witness MGT IP address (vmk0) available in group vars vsanWitnessHosts.vwa001.mgmtIpAddress e.g. 172.20.101.86) --witness-vsan-cidr (vSAN Witness management network CIDR  available in group vars vsanWitnessHosts.vwa001.mgmtNetworkCidr e.g. 172.20.101.0/24)
   ```

   When prompted enter the inputs for the following:

   - esxi host passwords
   - hosts vsan network gateway IP eg. 192.168.11.1
   - hosts vsan network vSAN CIDR eg. 192.168.11.0/24

   Ignore comment in step 5 ( 'The hosts in the Availability Zone 1 (AZ1) and the Availability Zone 2 (AZ2) are a part of the single...') from page 48 and monitor the progress of a triggered task described in steps 6 and 7.

7. After workflow completion, click on vSAN cluster, go to the 'Configure' tab, click on 'vSphere Availability'   and verify if the 'Host failures cluster tolerates' are set to the number of hosts in fault domain 1. If needed set the correct value.

8. Navigate to the vSAN cluster and click on 'Configure' tab. Under vSAN, click Fault Domains and edit the created fault domains names according to following naming convention: <location name>-m01-fd01 and <dr location name>-m01-fd01.

9. Navigate to Policies and Profiles -> VM Storage Policies and edit the vSAN Default Storage Policy associated with the management cluster. In the vSAN panel, under the Availability tab, set the following:
<br>-Site disaster tolerance to Dual Site Mirroring (stretched cluster)
<br>-Failure to tolerate is set to 1 failure Raid-1(Mirroring).

10. Create DRS VM/Host groups and DRS rules by running dedicated tasks form dhc-createStretchClusterMgmtDomain role. To execute the tasks run the playbook on ansible core VM with following command: 'ansible-playbook createStretchClusterMgmtDomain.yml --tags "createDrsGroupsAndRules,configureVmRestartPriority"'

11. Continue with the final validation from step 8 described in [vcfOnVxrailAdministering](vcfOnVxrailAdministering.pdf)
chapter 'Stretch a Cluster for NSX-V in VMware Cloud Foundation 3.9'
