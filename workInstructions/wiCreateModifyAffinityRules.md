# Using DRS Affinity Rules

## Table of Contents

- [Using DRS Affinity Rules](#using-drs-affinity-rules)
  - [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
  - [Related Documents](#related-documents)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Create a Host DRS Group](#create-a-host-drs-group)
    - [Procedure](#procedure)
    - [What to do next](#what-to-do-next)
  - [Create a Virtual Machine DRS Group](#create-a-virtual-machine-drs-group)
    - [Procedure](#procedure-1)
  - [VM-Host Affinity Rules](#vm-host-affinity-rules)
  - [Create a VM-Host Affinity Rule](#create-a-vm-host-affinity-rule)
    - [Prerequisites](#prerequisites)
    - [Procedure](#procedure-2)
  - [Using VM-Host Affinity Rules](#using-vm-host-affinity-rules)
  - [VM-VM Affinity Rules](#vm-vm-affinity-rules)
  - [Create a VM-VM Affinity Rule](#create-a-vm-vm-affinity-rule)
    - [Procedure](#procedure-3)
  - [VM-VM Affinity Rule Conflicts](#vm-vm-affinity-rule-conflicts)

# Changelog

| Version | Date | Description | Author |
| --- | --- | --- | --- |
| 0.1 | 21/02/2022 | First version | Berte Petru |
| 0.2 | 01/03/2022 | Adjstment of the documentation and removal of the pictures | Radoslaw Dabrowski |

## Related Documents

[Using DRS Affinity Rules](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.resmgmt.doc/GUID-FF28F29C-8B67-4EFF-A2EF-63B3537E6934.html)

## Introduction

### Purpose

Create or modify VM affinity rules in vCenter.

### Audience

- VCS Engineers
- VCS Operations

### Scope

You can control the placement of virtual machines on hosts within a cluster by using affinity rules.

You can create two types of rules:

[VM-Host Affinity Rules](#vm-host-affinity-rules)

- Used to specify affinity or anti-affinity between a group of virtual machines and a group of hosts. An affinity rule specifies that the members of a selected virtual machine DRS group can or must run on the members of a specific host DRS group. An anti-affinity rule specifies that the members of a selected virtual machine DRS group cannot run on the members of a specific host DRS group.

[VM-VM Affinity Rules](#vm-vm-affinity-rules)

- Used to specify affinity or anti-affinity between individual virtual machines. A rule specifying affinity causes DRS to try to keep the specified virtual machines together on the same host, for example, for performance reasons. With an anti-affinity rule, DRS tries to keep the specified virtual machines apart, for example, so that when a problem occurs with one host, you do not lose both virtual machines.

When you add or edit an affinity rule, and the cluster's current state is in violation of the rule, the system continues to operate and tries to correct the violation. For manual and partially automated DRS clusters, migration recommendations based on rule fulfillment and load balancing are presented for approval. You are not required to fulfill the rules, but the corresponding recommendations remain until the rules are fulfilled.

To check whether any enabled affinity rules are being violated and cannot be corrected by DRS, select the cluster's **DRS** tab and click **Faults**. Any rule currently being violated has a corresponding fault on this page. Read the fault to determine why DRS is not able to satisfy a particular rule. Rules violations also produce a log event.

**Note**: VM-VM and VM-Host affinity rules are different from an individual host’s CPU affinity rules.

## Create a Host DRS Group

A VM-Host affinity rule establishes an affinity (or anti-affinity) relationship between a virtual machine DRS group with a host DRS group. You must create both of these groups before you can create a rule that links them.

### Procedure

1. Browse to the cluster in the vSphere Client.
2. Click the **Configure** tab.
3. Under **Configuration**, select **VM/Host Groups** and click Add.
4. In the **Create VM/Host Group** dialog box, type a name for the group.
5. Select **Host Group** from the **Type** drop down box and click **Add**.
6. Click the check box next to a host to add it. Continue this process until all desired hosts have been added.
7. Click **OK**.

### What to do next

Using this host DRS group, you can create a VM-Host affinity rule that establishes an affinity (or anti-affinity) relationship with an appropriate virtual machine DRS group.

## Create a Virtual Machine DRS Group

Affinity rules establish an affinity (or anti-affinity) relationship between DRS groups. You must create DRS groups before you can create a rule that links them.

### Procedure

1. Browse to the cluster in the vSphere Client.
2. Click the **Configure** tab.
3. Under **Configuration**, select **VM/Host Groups** and click **Add**.
4. In the **Create VM/Host Group** dialog box, type a name for the group.
5. Select **VM Group** from the **Type** drop down box and click **Add**.
6. Click the check box next to a virtual machine to add it. Continue this process until all desired virtual machines have been added.
7. Click **OK**.

## VM-Host Affinity Rules

A VM-Host affinity rule specifies whether or not the members of a selected virtual machine DRS group can run on the members of a specific host DRS group.

Unlike a VM-VM affinity rule, which specifies affinity (or anti-affinity) between individual virtual machines, a VM-Host affinity rule specifies an affinity relationship between a group of virtual machines and a group of hosts. There are 'required' rules (designated by "must") and 'preferential' rules (designated by "should".)

**Note:** A VM-Host affinity rule includes the following components.

- One virtual machine DRS group.
- One host DRS group.
- A designation of whether the rule is a requirement ("must") or a preference ("should") and whether it is affinity ("run on") or anti-affinity ("not run on").

Because VM-Host affinity rules are cluster-based, the virtual machines and hosts that are included in a rule must all reside in the same cluster. If a virtual machine is removed from the cluster, it loses its DRS group affiliation, even if it is later returned to the cluster.

## Create a VM-Host Affinity Rule

You can create VM-Host affinity rules to specify whether or not the members of a selected virtual machine DRS group can run on the members of a specific host DRS group.

### Prerequisites

Create the virtual machine and host DRS groups to which the VM-Host affinity rule applies.

### Procedure

1. Browse to the cluster in the vSphere Client.
2. Click the **Configure** tab.
3. Under **Configuration**, click **VM/Host Rules**.
4. Click **Add**.
5. In the **Create VM/Host Rule** dialog box, type a name for the rule.
6. From the **Type** drop down menu, select **Virtual Machines to Hosts**.
7. Select the virtual machine DRS group and the host DRS group to which the rule applies.
8. Select a specification for the rule:

   - **Must run on hosts in group**. Virtual machines in VM Group 1 must run on hosts in Host Group A.
   - **Should run on hosts in group**. Virtual machines in VM Group 1 should, but are not required, to run on hosts in Host Group A.
   - **Must not run on hosts in group**. Virtual machines in VM Group 1 must never run on host in Host Group A.
   - **Should not run on hosts in group**. Virtual machines in VM Group 1 should not, but might, run on hosts in Host Group A.

9. Click **OK**.

## Using VM-Host Affinity Rules

You use a VM-Host affinity rule to specify an affinity relationship between a group of virtual machines and a group of hosts. When using VM-Host affinity rules, you should be aware of when they could be most useful, how conflicts between rules are resolved, and the importance of caution when setting required affinity rules.

If you create more than one VM-Host affinity rule, the rules are not ranked, but are applied equally. Be aware that this has implications for how the rules interact. For example, a virtual machine that belongs to two DRS groups, each of which belongs to a different required rule, can run only on hosts that belong to both of the host DRS groups represented in the rules.

When you create a VM-Host affinity rule, its ability to function in relation to other rules is not checked. So it is possible for you to create a rule that conflicts with the other rules you are using. When two VM-Host affinity rules conflict, the older one takes precedence and the newer rule is disabled. DRS only tries to satisfy enabled rules and disabled rules are ignored.

DRS, vSphere HA, and vSphere DPM never take any action that results in the violation of required affinity rules (those where the virtual machine DRS group 'must run on' or 'must not run on' the host DRS group). Accordingly, you should exercise caution when using this type of rule because of its potential to adversely affect the functioning of the cluster. If improperly used, required VM-Host affinity rules can fragment the cluster and inhibit the proper functioning of DRS, vSphere HA, and vSphere DPM.

A number of cluster functions are not performed if doing so would violate a required affinity rule.

- DRS does not evacuate virtual machines to place a host in maintenance mode.
- DRS does not place virtual machines for power-on or load balance virtual machines.
- vSphere HA does not perform failovers.
- vSphere DPM does not optimize power management by placing hosts into standby mode.

To avoid these situations, exercise caution when creating more than one required affinity rule or consider using VM-Host affinity rules that are preferential only (those where the virtual machine DRS group 'should run on' or 'should not run on' the host DRS group). Ensure that the number of hosts in the cluster with which each virtual machine is affined is large enough that losing a host does not result in a lack of hosts on which the virtual machine can run. Preferential rules can be violated to allow the proper functioning of DRS, vSphere HA, and vSphere DPM.

**Note**: You can create an event-based alarm that is triggered when a virtual machine violates a VM-Host affinity rule. Add a new alarm for the virtual machine and select **VM is violating VM-Host Affinity Rule** as the event trigger.

## VM-VM Affinity Rules

A VM-VM affinity rule specifies whether selected individual virtual machines should run on the same host or be kept on separate hosts. This type of rule is used to create affinity or anti-affinity between individual virtual machines that you select.

When an affinity rule is created, DRS tries to keep the specified virtual machines together on the same host. You might want to do this, for example, for performance reasons.

With an anti-affinity rule, DRS tries to keep the specified virtual machines apart. You could use such a rule if you want to guarantee that certain virtual machines are always on different physical hosts. In that case, if a problem occurs with one host, not all virtual machines would be placed at risk.

## Create a VM-VM Affinity Rule

You can create VM-VM affinity rules to specify whether selected individual virtual machines should run on the same host or be kept on separate hosts.

**Note**: If you use the vSphere HA Specify Failover Hosts admission control policy and designate multiple failover hosts, VM-VM affinity rules are not supported.

### Procedure

1. Browse to the cluster in the vSphere Client.
2. Click the **Configure** tab.
3. Under **Configuration**, click **VM/Host Rules**.
4. Click **Add**.
5. In the **Create VM/Host Rule** dialog box, type a name for the rule.
6. From the **Type** drop-down menu, select either **Keep Virtual Machines Together** or **Separate Virtual Machines**.
7. Click **Add**.
8. Select at least two virtual machines to which the rule will apply and click **OK**.
9. Click **OK**.

## VM-VM Affinity Rule Conflicts

You can create and use multiple VM-VM affinity rules, however, this might lead to situations where the rules conflict with one another.

If two VM-VM affinity rules are in conflict, you cannot enable both. For example, if one rule keeps two virtual machines together and another rule keeps the same two virtual machines apart, you cannot enable both rules. Select one of the rules to apply and disable or remove the conflicting rule.

When two VM-VM affinity rules conflict, the older one takes precedence and the newer rule is disabled. DRS only tries to satisfy enabled rules and disabled rules are ignored. DRS gives higher precedence to preventing violations of anti-affinity rules than violations of affinity rules.
