# Document for MSCC Integration with VCS

# Contents

- [Document for MSCC Integration with VCS](#document-for-mscc-integration-with-vcs)
- [Contents](#contents)
- [List of Changes](#list-of-changes)
- [Introduction](#introduction)
  - [Purpose](#purpose)
  - [Audience](#audience)
  - [Scope](#scope)
- [Integration](#integration)
  - [MSCC Framework](#mscc-framework)
  - [Architecture](#architecture)
    - [Architecture Diagram](#architecture-diagram)
    - [MSCC Structure](#mscc-structure)
    - [VCS Structure](#vcs-structure)
  - [Integrate with vCenter](#integrate-with-vcenter)
  - [Integrate with Aria Automation](#integrate-with-aria-automation)
  - [Integrate with Ansible](#integrate-with-ansible)

# List of Changes

The following changes have been made to this document.

| Date       | Change Detail                                      | Author         |
| ---------- | -------------------------------------------------- | -------------- |
| 02-01-2025 | Initial document explaining MSCC integration with VCS                  | Aswin Arumugam |

# Introduction

## Purpose

The purpose of this document is to provide an holistic view about the integration of MSCC framework with VMware Cloud Service (VCS) product.

## Audience

This is intended to Engineers and DevSecOps team for understanding the connection flow of MSCC with VCS.

## Scope

The document includes integration of various components like vCenter, Ansible and Aria Autamation(vRA) from MSCC.

# Integration

## MSCC Framework

MSCC Framework is a direct customer facing front end portal. Customers use this for requesting various Day-0,Day1 and Day-2 activities. Once requested a service now request is raised and the action is taken accordingly.

This MSCC framework is built/running on Kubernetes containers. It is currently deployed in Microsfot Azure Cloud. So we are connecting it from Azure portal to VCS.

There is a Cross-plane deployed in these containers which would help us installing providers that will ultimately connect to the VCS components.

## Architecture

### Architecture Diagram

![image](https://github.com/user-attachments/assets/eef031cd-4286-4f42-94c2-0d63462fb674)

The above diagram shows how we are connecting with the Azure Portal.

### MSCC Structure

MSCC application is deployed as a container in the Kubernetes cluster in Microsoft Azure (subscription ). Subnet chosen for this POC is 10.9.132.128

Firewall Opening Request from the Azure is opened by MSCC team.

Security team will ask Azure Security report for the subscription. Once it is green the firewall would be opened.

### VCS Structure

We will be connecting to 3 of the VCS components from MSCC.

**Components:**

1. vCenter
2. Aria Automation
3. Ansible

At first we will have to map these components with Private IP address. This is done by environment team.

[Sample Request done for POC](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14301)

| Appliance      | NX3 IP                                      | NAT IP         | Port |
| ---------- | -------------------------------------------------- | -------------- | ---- |
| Aria Automation | 172.22.61.130  | 10.196.215.60 | 443 |
| Ansible | 172.22.60.37  | 10.196.215.61 | 22 |
| Aria Automation | 172.22.48.20  | 10.196.215.62 | 443 |

Firewall Opening request from/to VCS is opened with the above details by the environment team for VDOM firewall.

Sample Request done for POC:  **CHG002818613**

Source IP: 10.9.132.128/28

**User-Creation:**

Ideally to connect to the components we have created a service account dedicated for this. This service account will act as a common one for all the 3 components.

Service Account Name: svc-gre32-mscc01.nx3dhc01.next

This has the previlages to login and do the required actions in the components

## Integrate with vCenter

We can approach this connection in two methods from Cross-Plane(MSCC)

1. vCenter Cross plane provider
2. Terraform Cross Plane Provider

**vCenter Cross Plane Provider**

A third party provider company named Ankasoft has developed a "Connection Providers named provider-vsphere" which is installed in the MSCC Cross Plane. Then MSCC team will be writting a Cloud Provider config that will be applied against our vCenter components.

**Provider Name:** provider-vsphere

For the POC we deployed a VM through the provider. The composition and the claim will have the necessary details like

1. vCenter Details
2. Credentials
3. Datacenter name
4. Cluster Name
5. Network to be used
6. Template Name
7. Server Name

Once the claim is run we will be able to see the deployment running in the vCenter which will create the VM with the name provided and in the cluster specified from the template name.

Verification of the success would be then checked again from the Cross-plane. And this VM would be managed as a deployment config in the Cross-Plane kubernetes.

**Various Scenarios tested:**

1) Ubuntu VM deployed with 1 network interface
2) Ubuntu VM deployed with 2 network interface
3) VM deployed with CPU and Hot add enabled
4) With various Memory and CPU sizes.
5) With hard disk attached to it.
6) VM without image.

[vSphere Provider Link](https://marketplace.upbound.io/providers/ankasoftco/provider-vsphere)

**Terraform Cross Plane Provider**

There is also a terraform providers which can also connect to the vCenter using the above mentioned details. It is also tested.

**Provider Name:** provider-terraform

[Terraform Provider Link](https://marketplace.upbound.io/providers/upbound/provider-terraform)

## Integrate with Aria Automation

There is a Provider developed by Ankasoft which can be installed in Cross Plane that enables connection to Aria Automation(vRA).

**Provider Name:** provider-jet-vra

Proper permission is set in the Aria Automation for the service account.

1. Get the tokens for login to the Aria Automation
2. Login to Aria Automation and ideally deploy a blueprint from Service Catalog.
3. Execute the Aria Automation related SSR's.

[vRA Provider Link](https://marketplace.upbound.io/providers/ankasoftco/provider-jet-vra)

## Integrate with Ansible

There is a Provider developed by crossplane-contrib which is installed in Cross Plane.

**Provider Name:** provider-ansible

When the claim is raised it will connect to the ansible server and triger the playbook.

By default when an environment is created the playbooks related to that environment version is copied in /opt/dhc" folder.

Ideally we can use it for deploying the required playbooks.

This will get refreshed when there is an upgrade in the environment to higher versions.

**(830-std) $ ls -ld manage deploy/**

**drwxrwxr-x 7 next next  8192 Oct 17 12:56 deploy/**

**drwxrwxr-x 9 next next 16384 Dec  3 14:09 manage**

**(830-std) :/opt/dhc$ pwd**

**/opt/dhc**

All the users would be able to run the playbook from this location.

[Ansible Provider Link](https://marketplace.upbound.io/providers/crossplane-contrib/provider-ansible)
