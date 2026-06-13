# VCS Create Snapshot

## Table of Contents

- [VCS Create Snapshot](#vcs-create-snapshot)
  - [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
- [Working with snapshots](#working-with-snapshots)
  - [Snapshot creation](#snapshot-creation)
  - [Snapshot Verification](#snapshot-verification)

# Changelog

| Version | Date       | Description              | Author          |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1 | 28/02/2022 | First version            | Dayanand Palkar |
| 0.2 | 16/03/2022 | Small adjustments to the structure of the document | Radoslaw Dabrowski |

## Introduction

### Purpose

Create virtual machine snapshots.

### Audience

- VCS Operations

### Scope

- Creating and verifying snapshots

# Working with snapshots

## Snapshot creation

1. Right-click the virtual machine in the inventory and click Take Snapshot

    > **Note:** To locate a virtual machine: Select a datacenter, folder, cluster, resource pool, host, or vApp and then click the Related Objects tab and click Virtual Machines.

2. Enter a **name** for the snapshot
3. Enter a **description** for the snapshot. This step is optional
4. Select the **Snapshot** the virtual machine’s memory option to capture the memory of the virtual machine. This step is optional
5. Select the **Quiesce guest file system** (Needs VMware Tools installed) option to pause running processes on the guest operating system, so that file system contents are in a known consistent state when you take the snapshot. This step is optional

    > **Note:** Quiesce the virtual machine files only when the virtual machine is powered on and you do not have to capture the virtual machine's memory

6. Click **OK**

## Snapshot Verification

1. After taking the snapshot, search the VM again in vCenter console
2. Then right click the **VM** and click on **Snapshots -> Manage Snapshots**
3. Then click on the **Snapshots** tab and ensure that the latest snapshot is visible
