# vSphere IaaS Control Plane in DHC management stack -  LLD

## Changelog

 |    Date    |  TOS   | Issue   | Author | Description |
 |------------|---------|-----------|--------|--------|
 | 10.03.2025 |  DHC 2.1   |   VCS-12866     | Lukasz Tomaszewski | Initial version |

## Introduction

vSphere IaaS Control Plane (formerly vSphere with Tanzu) on DHC transforms vSphere into a platform for running Kubernetes workloads natively on the hypervisor layer. When enabled on a vSphere cluster, vSphere with Tanzu provides the capability to run Kubernetes workloads directly on ESXi hosts and to create upstream Kubernetes clusters within dedicated resource pools. This LLD covers integration with management cluster only.

## Purpose

The purpose of this document is to provide detailed design and architectural guidance required to implement vSphere IaaS Control Plane in DHC management stack.
The document aims to provide insights on the underlying network architecture for vSphere IaaS Control Plane.

## Audience

This document is intended for Atos Cloud Services Engineers and Architects responsible for implementation of vSphere IaaS Control Plane in DHC and maintenance of same.

## Scope

LLD is intended to cover below components and domains:

- vSphere IaaS Control Plane components
- vSphere IaaS Control Plane - deployment options
- vSphere IaaS Control Plane - storage
- vSphere IaaS Control Plane - networking
- vSphere IaaS Control Plane - namespaces
  - identity and access management
  - content library
  - VM classes
  - storage classes
- vSphere IaaS Control Plane - monitoring

## Related Documents

This document is a subset of Atos Technology Lifecycle Management (ATLM) artifacts. All documents are stored in the DHC documentation repository.

| Document Name | Description |
|----|----|
| [wiVsphereTanzuMgmtBuildGuide](../workInstructions/wiVsphereTanzuMgmtBuildGuide) | This document contains guidance for deploying Tanzu |

## Vendor Related Documents

| Vendor | Document Name | Description |
|--------|---------------|-------------|
| VMware | <https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere-supervisor/8-0.html> | vSphere Supervisor 8.0 documentation |

## Requirement Levels

That document is following below principles in terms of requirements and design decisions.

| Term | Meaning |
|---|---|
| MUST | The definition is an absolute requirement of the specification. |
| MUST NOT | The definition is an absolute prohibition of the specification |
| SHOULD | There may exist valid reasons in particular circumstances to ignore a particular item, but the full implications must be understood and carefully weighed before choosing a different course |
| SHOULD NOT | There may exist valid reasons in particular circumstances when the particular behaviour is acceptable or even useful, but the full implications should be understood, and the case carefully weighed before implementing any behaviour described with this label |
| MAY | Any design decisions that are not classified as MUST and SHOULD or covering optional feature that is not general available for DHC product |

## Architecture Overview

The diagram below highlights the areas of the vSphere IaaS Control Plane architecture in scope of this LLD.

### Figure 1. vSphere IaaS control Plane Overview

![Vsphere With Tanzu](images/tanzuLLD/VsphereWithTanzu.png)

### vSphere IaaS Control Plane key components

- Supervisor cluster: When Workload Management is enabled on a vSphere cluster, it creates a Kubernetes layer within the ESXi hosts that are part of the cluster. A cluster that is enabled for Workload Management is called a Supervisor Cluster. Workloads are either run as native pods or as pods on upstream Kubernetes clusters created through the Tanzu Kubernetes Grid Service.

The Supervisor Cluster runs on top of an SDDC layer that consists of ESXi for compute, NSX-T and/or vSphere networking, and vSAN or another shared storage solution.

- vSphere Namespaces: A vSphere Namespace is a tenancy boundary within vSphere with Tanzu. A vSphere Namespace allows sharing vSphere resources (computer, networking, storage) and enforcing resource limits with the underlying objects such as Tanzu Kubernetes clusters. For each namespace, you configure role-based access control ( policies and permissions ), images library, and virtual machine classes.
- TKG (Tanzu Kubernetes Grid) Service: Tanzu Kubernetes Grid Service allows you to create and manage ubiquitous Kubernetes clusters on a VMware vSphere infrastructure using the Kubernetes Cluster API. The Cluster API provides declarative, Kubernetes-style API's to enable the creation, configuration, and management of the Tanzu Kubernetes cluster. vSphere 8.0 and above supports the ClusterClass API. The ClusterClass API is a collection of templates that define a cluster topology and configuration.
- TKG Cluster/TKC (Tanzu Kubernetes Cluster): Tanzu Kubernetes clusters are Kubernetes workload clusters in which your application workloads run. These clusters can be attached to SaaS solutions such as Tanzu Mission Control, Tanzu Observability, and Tanzu Service Mesh, which are part of Tanzu for Kubernetes Operations.
- vSphere Pods: vSphere with Tanzu introduces a new construct that is called vSphere Pod, which is the equivalent of a Kubernetes pod. A vSphere Pod is a Kubernetes Pod that runs directly on an ESXi host without requiring a Kubernetes cluster to be deployed. vSphere Pods are designed to be used for common services that are shared between workload clusters, such as a container registry.

A vSphere Pod is a VM with a small footprint that runs one or more Linux containers. Each vSphere Pod is sized precisely for the workload that it accommodates and has explicit resource reservations for that workload. It allocates the exact amount of storage, memory, and CPU resources required for the workload to run. vSphere Pods are only supported with Supervisor Clusters that are configured with NSX Data Center as the networking stack.

### vSphere IaaS Control Plane Architecture

#### Supervisor Conrol Plane - deployment options

With vSphere 8 and above, when you enable vSphere with Tanzu, you can configure either one-zone Supervisor mapped to one vSphere cluster or three-zone Supervisor mapped to three vSphere clusters. This reference architecture is based on single zone deployment of a Supervisor Cluster.

- Single-Zone Deployment of Supervisor

A supervisor deployed on a single vSphere cluster has three control plane VMs, which reside on the ESXi hosts part of the cluster. A single zone is created for the Supervisor automatically or you can use a zone that is created in advance. In a Single-Zone deployment, cluster-level high availability is maintained through vSphere HA and can scale with vSphere with Tanzu setup by adding physical hosts to vSphere cluster that maps to the Supervisor.
You can run workloads through vSphere Pods, Tanzu Kubernetes Grid clusters, and VMs when Supervisor is enabled with the NSX networking stack.

- Three-Zone Deployment of Supervisor
  
  Configure each vSphere cluster as an independent failure domain and map it to the vSphere zone. In a Three-Zone deployment, all three vSphere clusters become one Supervisor and can provide:
  
  - Cluster-level high availability to the Supervisor as vSphere cluster is an independent failure domain.
  - Distribute the nodes of Tanzu Kubernetes Grid clusters across all three vSphere zones and provide availability via vSphere HA at cluster level.
  - Scale the Supervisor by adding hosts to each of the three vSphere clusters.

|**Decision ID**|**Design Decision**|**Design Justification**|**Design Implication**|**Requirement Level**|
|---|---|---|---|---|
| TNZ001 | Single-Zone Deployment will be used | This option simplifies deployment and is in line with DHC architecture where each site ia a separate entity with HA protection based on vSphere cluster | Scaleing to Three-Zone deployment is not possible | MUST |

#### vSphere IaaS Control Plane - storage

vSphere IaaS Control Plane vSphere with Tanzu integrates with shared datastores available in the vSphere infrastructure. The following types of shared datastores are supported:

- vSAN
- VMFS
- NFS
- vVols

vSphere with Tanzu uses storage policies to integrate with shared datastores. The policies represent datastores and manage the storage placement of objects such as control plane VMs, container images, and persistent storage volumes.

Before you enable vSphere with Tanzu, create storage policies to be used by the Supervisor Cluster and namespaces. Depending on your vSphere storage environment, you can create several storage policies to represent different classes of storage.

vSphere with Tanzu is agnostic about which storage option you choose. For Kubernetes stateful workloads, vSphere with Tanzu installs the vSphere Container Storage Interface (vSphere CSI) to automatically provision Kubernetes persistent volumes for pods.

|**Decision ID**|**Design Decision**|**Design Justification**|**Design Implication**|**Requirement Level**|
|---|---|---|---|---|
| TNZ002 | vSAN storage will be used | vSAN is a primary storage within DHC | None | SHOULD |

#### vSphere IaaS Control Plane - networking

You can enable vSphere IaaS Control Plane in the following environments:

- vSphere backed with NSX Data Center Networking.
- vSphere backed with virtual Distributed Switch (VDS) Networking and HA proxy to provide Load Balancing capabilities.
- vSphere backed with virtual Distributed Switch (VDS) Networking and NSX Advanced Load Balancer to provide Load Balancing capabilities.

NSX provides network connectivity to the objects inside the Supervisor and external networks. Connectivity to the ESXi hosts within the cluster is backed by VLAN backed port groups.

The Supervisor cluster configured with NSX Networking either uses a distributed port group (routable to required infrastructure components such as vCenter, NSX manager, DNS , NTP and so on) or to NSX segment to provide connectivity to Kubernetes control plane VMs. Tanzu Kubernetes clusters and vSphere Pods have their networking provided by NSX segments. All hosts from the cluster, which are enabled for vSphere IaaS Control Plane, are connected to the distributed switch that provides connectivity to Kubernetes workloads and control plane VMs.

Network Requirements:

|**Network Type**|**Sample Recommendation**|**Description**|
| -------------- | ----------------------- | ------------- |
|Supervisor Management Network|/28 to allow for 5 IPs and  future expansion.|Network to host the supervisor VMs. It can be a VLAN backed VDS Port group or pre-created NSX segment. |
|Ingress IP range|/24, 254 address|Each service type Load Balancer deployed will consume 1 IP address. |
|Egress IP range|/24|Each vSphere namespace consumes 1 IP address for the SNAT egress. |
|Namespace/POD network CIDR|/20 <br>By default, it is used in /28 blocks by workload. |Allocate IP address to workload attached to supervisor namespace segments.|
|Supervisor Service CIDR|/23|Network from which IPs for Kubernetes ClusterIP Service will be allocated.|

Firewall Recommendations:

The following table provides a list of firewall rules based on the assumption that there is no firewall within a subnet or VLAN.

|**Source**|**Destination**|**Protocol:Port**|**Description**|
| ----- | ------ | ----- | ------ |
|vCenter|Supervisor Network|TCP:6443|Allows vCenter to manage the supervisor VMs.|
|vCenter|Supervisor Network|TCP:22|Allows platform administrators to connect to VMs through vCenter.|
|vCenter|Internet Proxy|TCP:3128|Allows vCenter to access internet (Subscribed Content Library).|
|NSX-T Manager|Internet Proxy|TCP:3128|Allows NSX-T to access internet (NSX-T Application Plaform requirement).|
|ESXi Hosts|Internet Proxy|TCP:3128|Allows ESXi hosts to access internet (required to download container image if you use VSphere Pods).|
|Ansible|vCenter|TCP:5480|Allows Ansible to configure internet proxy on vCenter Appliance.|

Design decisions:

|**Decision ID**|**Design Decision**|**Design Justification**|**Design Implication**|**Requirement Level**|
|---|---|---|---|---|
| TNZ003 | vDS Networking will be used for internal networks (management) | Simplifies deployment and management | Lack of microsegmentation | MUST |
| TNZ004 | NSX-T Data Center Networking will be used for external networks (ingress, egress)| NSX-T is a primary storage within DHC | None |MUST |

#### vSphere Namespaces

A vSphere Namespace provides the runtime environment for TKG clusters on Supervisor. To provision a TKG cluster, you first configure a vSphere namespace with users, roles, permissions, compute, storage, content library, and assign virtual machine classes. All these configurations are inherited by TKG clusters deployed in that namespace.

When you create a vSphere namespace, a network segment is created which is derived from the Namespace Network configured in Supervisor. While creating vSphere namespace, you have the option to override cluster network settings. Choosing this option lets you customize the vSphere namespace network by adding Ingress, Egress, and Namespace network CIDR (unique from the Supervisor and from any other vSphere namespace).

|**Decision ID**|**Design Decision**|**Design Justification**|**Design Implications**|**Requirement Level**|
| ----- | ------ | ----- | ------ | ----- |
|TNZ005|Create dedicated namespace to environment specific.|Segregate prod/dev/test cluster via assigning them to dedicated namespaces.|Clusters created within the namespace share the same access policies/quotas/network & storage resources.| MUST |
|TNZ006|Register external IDP with Supervisor or AD/LDAP with vCenter SSO.|Limit access to namespace based on role of users or groups.|External AD/LDAP needs to be integrated with vCenter or SSO Groups need to be created manually.| MUST |
|TNZ007|Enable namespace self-service|Enables Devops users to create namespace in self-service manner.|The vSphere administrator must publish a namespace template to LDAP users or groups to enable them to create a namespace.| MAY |

##### Identity and access management

vSphere with Tanzu supports the following two identity providers:

- **vCenter Single Sign-On:** This is the default identity provider that is used to authenticate with vSphere with Tanzu environment, including the Supervisors and Tanzu Kubernetes Grid Clusters. vCenter Single Sign-On (SSO) provides authentication for vSphere infrastructure and can integrate with AD/LDAP systems.

  To authenticate using vCenter SSO, use vSphere plug-in for kubectl. Once authenticated, use kubectl to declaratively provision and manage the lifecycle of TKG clusters, deploy TKG cluster workloads.

- **External Identity Provider:** You can configure a Supervisor with an external identity provider and support the [OpenID Connect protocol](https://openid.net/connect/). Once connected, the Supervisor functions as an OAuth 2.0 client, and uses the [Pinniped](https://pinniped.dev/) authentication service to connect to Tanzu Kubernetes Grid clusters by using the Tanzu CLI. Each Supervisor instance can support one external identity provider. For more information about the list of supported OIDC providers, see [Configure an External IDP](https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere-supervisor/7-0/configure-an-external-idp-for-use-with-tkg-service-clusters.html).

The Tanzu Kubernetes Grid (informally known as TKG) cluster permissions are set and scoped at the vSphere Namespace level. When permissions are set for Namespace, including identity source, users & groups, and roles, all these permissions apply to any TKG cluster deployed within that vSphere Namespace.

###### Roles and Permissions

TKG Clusters supports the following three roles:

- Viewer
- Editor
- Owner

These permissions are assigned and scoped at vSphere Namespace.

|**Permission**|**Description**|
| ------ | ------ |
|Can view|Read-only access to TKG clusters provisioned in that vSphere Namespace.|
|Can edit|Create, read, update, and delete TKG clusters in that vSphere Namespace.|
|Owner|Can administer TKG clusters in a vSphere Namespace, and can create and delete additional vSphere Namespaces using kubectl.|

|**Decision ID**|**Design Decision**|**Design Justification**|**Design Implications**|**Requirement Leve**|
| ------- | -------- | ----- | ----- | ----- |
|TNZ008| <administrator@vsphere.local> must be set as owner | Allows to access and manage TKG cluster by local vSphere administrator via API/CLI (Ansible playbooks)  | None | MUST |
|TNZ009| platformadministrator role must be set as editor | Allows to access TKG cluster by AD role memmbers via API/CLI | None | MUST |

##### Content library

|**Decision ID**|**Design Decision**|**Design Justification**|**Design Implications**| Requirement Level |
| ------- | -------- | ----- | ----- | ----- |
|TNZ0010|Create a Subscribed Content Library. |<p>Subscribed Content Library can automatically pull the latest OVAs used by the Tanzu Kubernetes Grid Service to build cluster nodes.</p><p>Using a subscribed content library facilitates template management as new versions can be pulled by initiating the library sync.</p>|<p>Local Content Library would require manual upload of images, suitable for air-gapped or Internet restricted environment.</p>| MUST |

##### VM Class

A VM class is a template that defines CPU, memory, and reservations for VMs. VM classes are used for VM deployment in a Supervisor Namespace. VM classes can be used by standalone VMs that run in a Supervisor Namespace, and by VMs hosting a Tanzu Kubernetes cluster.

  VM Classes in a vSphere with Tanzu are categorized into the following two groups:

- **Guaranteed:** This class fully reserves its configured resources.
- **Best-effort:** This class allows to be overcommitted.

  vSphere with Tanzu offers several default VM classes. You can either use the default VM classes, or create customized VM classes based on the requirements of the application. The following table explains the default VM classes that are available in vSphere with Tanzu:
  
  |**Class**|**CPU**|**Memory(GB)**|**Reserved CPU and Memory**|
  | ------- | ----- | ------------ | ----------- |
  |best-effort-xsmall|2|2|No|
  |best-effort-small|2|4|No|
  |best-effort-medium|2|8|No|
  |best-effort-large|4|16|No|
  |best-effort-xlarge|4|32|No|
  |best-effort-2xlarge|8|64|No|
  |best-effort-4xlarge|16|128|No|
  |best-effort-8xlarge|32|128|No|
  |guaranteed-xsmall|2|2|Yes|
  |guaranteed-small|2|4|Yes|
  |guaranteed-medium|2|8|Yes|
  |guaranteed-large|4|16|Yes|
  |guaranteed-xlarge|4|32|Yes|
  |guaranteed-2xlarge|8|64|Yes|
  |guaranteed-4xlarge|16|128|Yes|
  |guaranteed-8xlarge|32|128|Yes|

|**Decision ID**|**Design Decision**|**Design Justification**|**Design Implications**| Requirement Level |
| ----- | ------ | ----- | ------ | ----- |
|TNZ011|Use guaranteed VM Class for production cluster.|CPU and Memory limits configured on vSphere Namespace have impact on TKG cluster if deployed using the guaranteed VM Class type.|Consume more infrastructure resources and contention might occur.| SHOULD |

##### Storage Classes

A StorageClass allows the administrators to describe the classes of storage that they offer. Different storage classes can map to meet quality-of-service levels, to backup policies, or to arbitrary policies determined by the cluster administrators. The policies representing datastore can manage storage placement of such components and objects as control plane VMs, vsphere Pod ephemeral disks, and container images. You might need policies for storage placement of persistent volumes and VM content libraries.

 You can deploy vSphere IaaS Control Plane with an existing default storage class or the vSphere administrator can define storage class objects (Storage policy) that let cluster users dynamically create PVC and PV objects with different storage types and rules.

  The following table provides recommendations for configuring VM Classes/Storage Classes in a vSphere with Tanzu environment.

  |**Decision ID**|**Design Decision**|**Design Justification**|**Design Implications**|**Requirement Level**|
  | --- | --- | --- | --- | --- |
  |TNZ012|Create custom Storage Classes/Profiles/Policies|To provide different levels of QoS and SLA for prod and dev/test K8s workloads. |Default Storage Policy might not be adequate if deployed applications have different performance and availability requirements. | MAY |
  |TNZ013|Create custom VM Classes|To facilitate deployment of K8s workloads with specific compute/storage requirements.|Default VM Classes in vSphere with Tanzu are not adequate to run a wide variety of K8s workloads. | MAY |

### vSphere IaaS Control Plane - monitoring

The VMware Aria Operations Management Pack for Kubernetes provides end-to-end visibility for Kubernetes and its resources. With Kubernetes becoming the platform of choice to run applications in enterprises, it is essential that an organization has the required tools for the IT teams to operationalize the Kubernetes platform. Whether Kubernetes is hosted on top of VMware’s Software Defined data center or in a native public cloud such as Amazon Web Services, Microsoft Azure, the central IT teams need full visibility into this new world to assure the performance and availability of business applications. These business applications can continue to use both virtual machines and containers for the foreseeable future.

Using VMware Aria Operations Management Pack for Kubernetes, you can visualize, monitor, and troubleshoot your Kubernetes ecosystem effectively. Some of the additional capabilities of this management pack are as follows:

- Supports various Kubernetes distributions like VMware Tanzu Kubernetes Grid Integrated, VMware Tanzu Kubernetes Grid, vSphere with Tanzu and Red Hat OpenShift
- Autodiscovery of clusters in VMware Tanzu Kubernetes Grid Integrated and VMware Tanzu Kubernetes Grid
- Autodiscovery of clusters provisioned on Amazon Web Services and vSphere using VMware Tanzu Mission Control (TMC)
- Complete visualization of Kubernetes cluster topology, including namespaces, clusters, replica sets, nodes, pods, and containers
- Mapping Kubernetes nodes with virtual machines to provide complete visibility into the Kubernetes environment
- Performance monitoring for Kubernetes clusters, with support for monitoring via cAdvisor and Prometheus
- Out-of-the-box dashboards and alerts to manage the Kubernetes environment

|**Decision ID**|**Design Decision**|**Design Justification**|**Design Implications**|**Requirement Level**|
| --- | --- | --- | --- | --- |
|TNZ014| Aria Operations with Kubernetes management pack will be used for monitoring solution | Aria Operations is a primary monitoring solution for DHC | None | MUST |
