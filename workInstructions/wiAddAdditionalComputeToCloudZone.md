# VCS add additional compute resources to the same cloud zone in vRA on-prem

## Table of Contents

- [VCS add additional compute resources to the same cloud zone in vRA on-prem](#vcs-add-additional-compute-resources-to-the-same-cloud-zone-in-vra-on-prem)
  - [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Create vCenter Resource Pools](#create-vcenter-resource-pools)
  - [Configure in vRA new compute resource tag for each resource pool](#configure-in-vra-new-compute-resource-tag-for-each-resource-pool)
  - [Add additional compute resources to same cloud zone](#add-additional-compute-resources-to-same-cloud-zone)
  - [Add clusters in blueprint](#add-clusters-in-blueprint)
  - [Configure Service Broker catalog item](#configure-service-broker-catalog-item)

# Changelog

| Date | Author       | Issue              | Description          |
| ------- | ---------- | ------------------------ | --------------- |
| 09.04.2024| Slabu Adriana | VCS-12501  |  Create initial version  |

## Introduction

### Purpose

Configure additional clusters into automation, in the same vRA on-prem Cloud Zone.

### Audience

- VCS Operations

### Scope

- add and configure from vCenter side the required configuration so that VMs can be deployed in the cluster.
- add and configure from vRA on-prem side, additional clusters/compute resources in the same Cloud Zone.

## Create vCenter Resource Pools

Login to vCenter and right click on the desired cluster. From the drop-down list choose "New Resource Pool".

![create-RP1](images/wiAddAdditionalComputeToCloudZone/create-RP1.JPG)

A windows will open. Enter a name to the new Resource Pool according to the naming convention: < locationCode >-< type >-user-vm< clusterNumber > , eg. < locationCode >-c01-user-vm02.

![create-RP2](images/wiAddAdditionalComputeToCloudZone/create-RP2.JPG)

> NOTE: We leave the default settings: normal shares with expandable reservation and don't set any limit.

We create this Resource Pool per cluster so that VMs created via automation will automatically be deployed in it.

## Configure in vRA new compute resource tag for each resource pool

We wait approximately 15 minutes for vRA data collection to trigger and complete. This is because vRA automatically discovers vCenter resources for compute and storage.

We login to vRA on-prem by accessing vRA load balancer FQDN from the browser. The initial vRA portal will direct us to login via IDM with the local dhc account.

>Note: vRA on-prem FQDN naming convention: *https://< locationCode > vra001 < searchDomain >*.

Once logged in vRA on-prem, we choose Assembler -> Infrastructure -> Compute. On the last page, the clusters along with the newly created Resource Pools will be discovered.

![compute-RPs](images/wiAddAdditionalComputeToCloudZone/compute-RPs.JPG)

Select the cluster/resource pool which we need to add to the Cloud Zone. Afterwards the "TAGS" button will become visible above the list.

![compute-add-tag](images/wiAddAdditionalComputeToCloudZone/compute-add-tag.JPG)

Click on it and add the capability tag according to the naming convention: < parameter >:< value >, value being the Resource Pool name, eg. "computerp: < locationCode >-c01-user-vm02".

![compute-add-tag3](images/wiAddAdditionalComputeToCloudZone/compute-add-tag3.JPG)

The cluster/Resource Pool row will have at the end of it the added tag.

![compute-add-tag2](images/wiAddAdditionalComputeToCloudZone/compute-add-tag2.JPG)

We also need to check in Infrastructure -> Resources -> Storage to see the storage policies have been discovered.

![storage-policies](images/wiAddAdditionalComputeToCloudZone/storage-policies.JPG)

And we also check in Infrastructure -> Configure -> Storage Profiles to see the storage profiles have been added.

![storage-profiles](images/wiAddAdditionalComputeToCloudZone/storage-profiles.JPG)

## Add additional compute resources to same cloud zone

From Infrastructure -> Cloud Zones -> Compute: we choose "Manually select compute" and click "ADD" button.

![add-compute-to-cloudzone](images/wiAddAdditionalComputeToCloudZone/add-compute-to-cloudzone.JPG)

A new windows will open and we can select the compute resource: "cluster/Resource Pool" already discovered in the Compute section and already having the assigned tag. We click "ADD" button. the additional clusters will be listed along with the initial one in a similar way.

![add-compute-to-cloudzone2](images/wiAddAdditionalComputeToCloudZone/add-compute-to-cloudzone2.JPG)

![add-compute-to-cloudzone3](images/wiAddAdditionalComputeToCloudZone/add-compute-to-cloudzone3.JPG)

Once we add the additional clusters to the Cloud Zones, select "Save" so that the new configuration will be saved.

![add-compute-to-cloudzone4](images/wiAddAdditionalComputeToCloudZone/add-compute-to-cloudzone4.JPG)

## Add clusters in blueprint

From vRA on-prem, Assembler -> Design tab -> Templates, we choose the template in use, in our case "Deploy virtual machine".

![add-clusters-to-blueprint](images/wiAddAdditionalComputeToCloudZone/add-clusters-to-blueprint.JPG)

On the right column, "Code" section, we scroll down and search where the first cluster is added. In our case, the cluster was added in to  **cluster_tag**. We add the other additional clusters on the unordered list.

![add-clusters-to-blueprint1](images/wiAddAdditionalComputeToCloudZone/add-clusters-to-blueprint1.JPG)

We can reconfigure existing tags, if for example, the additional clusters are different from the first one. In our case, the 2 additional clusters are standalone, so VMs don't have Disaster Recovery enabled. In this case, we could add constraints to the tags, which can be found here:

![add-clusters-to-blueprint](images/wiAddAdditionalComputeToCloudZone/configure-tags-in-blueprint.JPG)

We must test the template before saving any versions. We do that by clicking on "TEST" button. A pop-up window will open and a form must be completed before hitting submit.

![test-blueprint](images/wiAddAdditionalComputeToCloudZone/test-blueprint.JPG)

![test-blueprint2](images/wiAddAdditionalComputeToCloudZone/test-blueprint2.JPG)

Testing the blueprint will check the syntax and component mappings of blueprint to ensure deployment viability. If test deployment is successful, we are ready to deploy our blueprint.

Once all the needed modifications are done, it's recommended to increase the version of your blueprint so it's possible to revert the changes. We can increase the version by clicking on the "VERSION" button at the bottom of the page.

![version-button](images/wiAddAdditionalComputeToCloudZone/version-button.JPG)

A pop-up window will open where we can increase the current version. We also have the option to release this version so that Service Broker can discover the new version as well.

![create-blueprint-vers](images/wiAddAdditionalComputeToCloudZone/create-blueprint-vers.JPG)

We can check the changes we've made and versions from "VERSION HISTORY" button.

![release-blueprint-to-broker](images/wiAddAdditionalComputeToCloudZone/release-blueprint-to-broker.JPG)

![release-blueprint-to-broker2](images/wiAddAdditionalComputeToCloudZone/release-blueprint-to-broker2.JPG)

## Configure Service Broker catalog item

We login to "Service Broker" in a similar way we logged in to "Assembler" and on the "Content&Policies" tab, we choose "Content Sources". On the right it will tell you how many templates are currently discovered and released from Assembler side.

![service-broker1](images/wiAddAdditionalComputeToCloudZone/service-broker1.JPG)

To be able to import another template or update it with a new version template release - we have to click on the blueprint already configured and hit validate. It will discover all the releases and additional templates from Design tab in vRA Assembler. Hit "SAVE & IMPORT" so that Catalog form request will be updated.

![service-broker2](images/wiAddAdditionalComputeToCloudZone/service-broker2.JPG)

From the same tab: "Content&Policies", if we choose "Content" on the left menu, we'll be able to access the custom form of the template so that we can further customize it and add conditions for the input fields of the form itself.

![service-broker3](images/wiAddAdditionalComputeToCloudZone/service-broker3.JPG)

We need to customize Service Broker request form to help the user fill in valid options and avoid wrong inputs. This is done from "Customize form" option from the hidden drop-down.

![service-broker5](images/wiAddAdditionalComputeToCloudZone/service-broker5.JPG)

For example, we might need to hide all DR related fields if "Enable DR protection on VM" checkbox is not checked. We do this by adding a conditional value, which interactively hide or show the DR option if checkbox is selected.

![DR-conditional-value](images/wiAddAdditionalComputeToCloudZone/DR-conditional-value.JPG)
