# Day-2 Action 'Clone VM' Deployment Guide

- [Day-2 Action 'Clone VM' Deployment Guide](#day-2-action-clone-vm-deployment-guide)
- [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
- [Requirements](#requirements)
- [Deployment Process](#deployment-process)
  - [Step 1: Create the 'Clone VM' Resource Action](#step-1-create-the-clone-vm-resource-action)
  - [Step 2: Validate the Resource Action Creation](#step-2-validate-the-resource-action-creation)
  - [Step 3: Enable the Policy for the 'Clone VM' Action](#step-3-enable-the-policy-for-the-clone-vm-action)
  - [Step 4: Final Validation](#step-4-final-validation)

# Changelog

| Date         | Author          | Description                                                    |
| :----------- | :-------------- | :------------------------------------------------------------- |
| 2025-10-01   | Lukasz Tworek | Creation of the deployment guide for the 'Clone VM' Day-2 action. |

## Introduction

### Purpose

The purpose of this document is to outline the steps required to deploy and activate the Day-2 action named 'Clone VM' in a vRealize Automation (vRA) environment. This action allows users to clone existing virtual machines directly from the vRA portal.

### Audience

- VCS Engineers
- VCS Operations

### Scope

This guide covers the deployment process for the 'Clone VM' action in vRA On-Premise environments using dedicated Ansible playbooks.

# Requirements

1. Access to the Ansible management server (e.g., `ans001`) where the playbooks are located.
2. The `createDay2Actions.yml` and `enableCloneVMAction.yml` playbooks must be available in the `/opt/dhc/manage` directory.
3. User credentials (e.g., in the `dasid@domain.next` format) with permissions to run playbooks and make changes in vRA.

# Deployment Process

The deployment process consists of two main stages: creating the Resource Action definition and activating the policy that makes this action available to users.

### Step 1: Create the 'Clone VM' Resource Action

The first step is to run the `createDay2Actions.yml` playbook. This playbook is responsible for creating a set of predefined Day-2 actions in vRA, including the action for cloning virtual machines.

1. Log in to the Ansible management server.
2. Navigate to the playbook directory:

    ```bash
    
    cd /opt/dhc/manage
    
    ```

3. Run the playbook using the following command:

   ```bash
   
    ansible-playbook createDay2Actions.yml
   
    ```

4. During the playbook execution, you will be prompted to provide the following information:
    - **Tenant Name** (`Please provide TENANT NAME`): Enter the name of the tenant for which the action is being deployed.
    - **Username and password**: Enter the credentials, for trigger the playbook and collect necesarry values.

The playbook will connect to vRA, obtain the necessary authorization tokens, and create the `CloneVMToPath` resource action based on the `cloneVM.yml` template.

### Step 2: Validate the Resource Action Creation

After the playbook completes successfully, it is recommended to verify that the resource action has been created correctly in vRA.

1. Log in to the vRA user interface and navigate to the **Assembler** service.
2. In the navigation menu, select **Design** -> **Resource Actions**.
3. In the list of actions, search for the newly created item. It should have the following attributes:

- **Name**: `{{tenant}}-CloneVM` (where `{{tenant}}` is the name you provided when running the playbook)
- **Display Name**: `Clone VM`
- **Resource Type**: `Cloud.vSphere.Machine`
- **Provider**: `vro-workflow`
- **Runnable Item**: `CloneVMToPath`

The presence of this action confirms that Step 1 was completed correctly.
[Clone VM Action Resource](images/wiEnableCloneDay2Action/cloneVM_action_resource.png)

### Step 3: Enable the Policy for the 'Clone VM' Action

Creating the resource action alone does not make it visible to users. To activate it, you must run the `enableCloneVMAction.yml` playbook, which will configure the corresponding policy in vRA.

1. On the Ansible management server, in the `/opt/dhc/manage` directory, run the second playbook:

   ```bash

    ansible-playbook enableCloneVMAction.yml

    ```

2. Similar to the first step, the playbook will prompt you for:

- **Tenant Name** (`Please provide TENANT NAME`): Enter the name of the tenant for which the action is being deployed
- **Username and password**: Enter the credentials, for trigger the playbook and collect necesarry values.

This playbook will modify the Day-2 policy in **Service Broker**, adding the permission to run the 'Clone VM' action for resources of type `Cloud.vSphere.Machine`.

### Step 4: Final Validation

The final step is to verify that the 'Clone VM' action is visible and available on a deployed virtual machine.

1. Log in to vRA and navigate to the **Service Broker** service.
2. Select **Deployments** and find any active deployment containing a virtual machine.
3. Click on the virtual machine to open its details, and then navigate to the **Actions** menu.
4. The **"Clone VM"** option should be visible in the list of available actions.

[Clone VM Action Visible](images/wiEnableCloneDay2Action/cloneVM_action.png)

The appearance of this option means that the entire deployment procedure was successful.
