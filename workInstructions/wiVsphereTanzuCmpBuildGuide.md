# List of Changes
  
| Version | Date       | Description      | Author       |
| ------- | ---------- | ---------------- | -------------|
| 0.1     | 04.06.2025 | First version    | Lukasz Tomaszewski |
| 0.2     | 29.10.2025 | Minor updates    | Lukasz Tomaszewski |
| 0.3     | 09.01.2026 | YAML formatting    | Lukasz Tomaszewski |
| 0.4     | 22.01.2026 | New chapter - Post deployment | Lukasz Tomaszewski |

## Introduction

### Purpose

Configure vSphere IaaS Control Plane (Tanzu) solution on compute cluster.

### Audience

- DHC Operations

### Scope

- Adjust DHC variables
- Run prerequisites playbook for Tanzu solution
- Deploy VSphere IaaS Control Plane (Supervisor)
- Enable Aria Operations monitoring (configure Aria Operations Kubernetes Adapter)
- Create TKG Cluster (additional operation)

# Related Documents

N/A

# Prerequisites

Before you run any playbook, make sure to checkout DHC-Manage, DHC-Firewall and DHC-Collections branches to: FEATURE/DHC-2.1 (or equivalent, i.e. DHC-2.1).

NOTE: required packages were added to DHC-Version-Matrix/DHC-2.0 branch, thus:

- ansible should be already built with required pip modules
- kubernetes management pack file for Aria Operations should be already in /opt/binaries

# Deploy procedure

## Adjust DHC variables

These are the only manual steps you need to perform before running automated tasks via ansible.

1. Login to Ansible host VM. Create /opt/dhcConfig/group_vars/all/tanzuConfig.yml file and populate with data:

Following variables are required, and need to be updated:

- tanzu.supervisor.workload.kubernetesNetwork.edgeCluster
- tanzu.supervisor.workload.kubernetesNetwork.t0Gateway
- tanzu.supervisor.workload.kubernetesNetwork.t1Gateway
- tanzu.supervisor.workload.kubernetesNetwork.ingressNetworkCidr
- tanzu.supervisor.workload.kubernetesNetwork.egressNetworkCidr
- tkg.resource_definition.spec.settings.network.proxy.noProxy
- tkg.resource_definition.spec.settings.network.proxy.httpProxy
- tkg.resource_definition.spec.settings.network.proxy.httpsProxy

```yaml
tanzu:
  supervisor:
    management: #for vcs001
    workload: #for vcs002
      apiServerDnsNames:
        - "{{ locationCode }}tnz002.{{ searchDomain }}"
      vCenter: "{{ mgmtDns.vcs002.name }}.{{ searchDomain }}"
      name: "{{ locationCode }}-c01-supervisor01"
      clusterName: "{{ locationCode }}-c01-cluster01"
      zoneName: "{{ locationCode }}-c01-zone01"
      controlPlaneStoragePolicy: vSAN Default Storage Policy
      ephemeralDisksStoragePolicy: vSAN Default Storage Policy
      imageCacheStoragePolicy: vSAN Default Storage Policy
      supervisorNetwork:
        networkMode: static
        network: SDDC-DPortGroup-Mgmt
        startingIpAddress: "{{ networkMgmt.cidr }}.80"
        subnetMask: "{{ networkMgmt.netmask }}"
        gateway: "{{ networkMgmt.cidr }}.{{ networkMgmt.gw }}"
        dnsServers:
          - "{{ mgmtDns.adc001.cidr }}.{{ mgmtDns.adc001.octet }}"
          - "{{ mgmtDns.adc002.cidr }}.{{ mgmtDns.adc002.octet }}"
        dnsSearchDomains:
          - "{{ searchDomain }}"
        ntpServers:
          - "{{ mgmtDns.adc001.cidr }}.{{ mgmtDns.adc001.octet }}"
          - "{{ mgmtDns.adc002.cidr }}.{{ mgmtDns.adc002.octet }}"
      kubernetesNetwork:
        vsphereDistributedSwitch:
        edgeCluster: "{{ locationCode }}ecn101" #replace with correct value (allign with networking team)
        t0Gateway: "{{ locationCode }}ecn101-t0-gw01" #replace with correct value (allign with networking team) - It must be T0 gateway, not VRF T0!
        t1Gateway: "{{ locationCode }}ecn101-t1-gw02" #replace with correct value (allign with networking team)
        dnsServers:
          - "{{ mgmtDns.adc001.cidr }}.{{ mgmtDns.adc001.octet }}"
          - "{{ mgmtDns.adc002.cidr }}.{{ mgmtDns.adc002.octet }}"
        natMode: true
        namespaceNetworkCidr: 10.244.0.0/20
        subnetPrefix: "28"
        serviceNetworkCidr: 10.96.0.0/23
        ingressSegmentName: tanzu-ingress-c01-seg01
        ingressNetworkCidr: x.x.x.x/24 #replace x.x.x.x/24 with correct value
        egressSegmentName: tanzu-egress-c01-seg01
        egressNetworkCidr: x.x.x.x/24 #replace x.x.x.x/24 with correct value
  namespaces:
    - namespace: ns-workload-01
      cluster: "{{ locationCode }}-c01-cluster01"
      description: Workload 01 namespace
      access_list:
        - domain: vsphere.local
          role: OWNER
          subject: Administrator
          subject_type: USER
        - domain: "{{ searchDomain }}"
          role: EDIT
          subject: role-{{ locationCode }}-g-platformadministrators
          subject_type: GROUP
      storage_specs:
        - policy: vSAN Default Storage Policy
      vm_service_spec:
        content_libraries:
          - TKR-sub
        vm_classes:
          - best-effort-8xlarge
          - guaranteed-large
          - guaranteed-medium
          - guaranteed-small
          - guaranteed-xsmall
          - best-effort-medium
          - best-effort-xsmall
          - best-effort-4xlarge
          - best-effort-small
          - guaranteed-xlarge
          - best-effort-2xlarge
          - best-effort-large
          - best-effort-xlarge
          - guaranteed-2xlarge
          - guaranteed-4xlarge
          - guaranteed-8xlarge
  tkg:
    - clusterName: tkg-workload-01
      resource_definition:
        apiVersion: run.tanzu.vmware.com/v1alpha3
        kind: TanzuKubernetesCluster
        metadata:
          name: tkg-workload-01
          namespace: ns-workload-01
        spec:
          topology:
            controlPlane:
              replicas: 1
              vmClass: best-effort-medium
              tkr:
                reference:
                  name: v1.29.4---vmware.3-fips.1-tkg.1 #to provide compatible Kubernetes versions follow the chapter: "Post deployment - TKG spec"
              storageClass: vsan-default-storage-policy
              volumes:
                - name: etcd
                  mountPath: /var/lib/etcd
                  capacity:
                    storage: 64Gi
                - name: containerd
                  mountPath: /var/lib/containerd
                  capacity:
                    storage: 64Gi
            nodePools:
              - replicas: 1
                name: worker
                vmClass: best-effort-small
                storageClass: vsan-default-storage-policy
                volumes:
                  - name: etcd
                    mountPath: /var/lib/etcd
                    capacity:
                      storage: 64Gi
                  - name: containerd
                    mountPath: /var/lib/containerd
                    capacity:
                      storage: 64Gi
        settings:
          storage:
            defaultClass: vsan-default-storage-policy
          network:
            proxy:
              httpProxy: http://{{ mgmtDns.pxy001.cidr }}.{{ mgmtDns.pxy001.octet }}:3128 #replace with customer proxy (if access to external repositories is needed)
              httpsProxy: http://{{ mgmtDns.pxy001.cidr }}.{{ mgmtDns.pxy001.octet }}:3128 #replace with customer proxy (if access to external repositories is needed)
              noProxy: [10.244.0.0/19,10.96.0.0/23,ingressNetworkCidr,egressNetworkCidr,.next] #replace ingressNetworkCidr and egressNetworkCidr with correct values
```

2.Login to Ansible host VM. Update /opt/dhcConfig/group_vars/all/platformConfig.yml with Tanzu license:

  Following variable is required, and need to be addeded:

- licenses.tanzuLicense.key (source file: platformConfig.yml)

  ```yaml
  # licenses variable (line ~516,) add license for tanzu:
    ...
    tanzuLicense:
      key: "xxxxx-xxxxx-xxxxx-xxxxx-xxxxx"
  ```

## Automated deployment steps

NOTE: All playbooks must be run from /opt/dhc/manage folder.

1. Login to Ansible host VM and execute tanzuMgmtPrerequisites playbook:

    ```bash
    ansible-playbook tanzuCmpPrerequisites.yml
    ```

    or, if you want to control separate tasks

    ```bash
    ansible-playbook tanzuCmpPrerequisites.yml --tags=tag1,tag2
    ```

    Available tags for this playbook are:

    - dfw - to update distributed firewall
    - pxy - to update squid configuration - whitelist and acls
    - vcs - to enable http proxy for vcsa, create subscribed content library
    - nsx - to configure proxy on nsx, create segments and t1 router, append tag to edge cluster
    - ops - to install managment pack for kubernetes on aria operations
    - bil - to configure billing prerequisites

2. Login to Ansible host VM and execute tanzuMgmtSupervisor playbook:

    ```bash
    ansible-playbook tanzuCmpSupervisor.yml
    ```

    or, if you want to control separate tasks

    ```bash
    ansible-playbook tanzuCmpSupervisor.yml --tags=tag1,tag2
    ```

    Available tags for this playbook are:

    - supervisor - to enable supervisor cluster and register dhs record for tanzu supervisor
    - kubectl - to install kubectl on ansible (ans001) host
    - license - to apply tanzu license if evaluation license is set (plese note that this tag applies only when supervisor was enabled)

3. Login to Ansible host VM and execute tanzuEnableSupervisorMonitoring playbook:

    This playbook configures Kubernetes Adapter on Aria Operations, to enable more detailed monitoring for Tanzu clusters.

    ```bash
    ansible-playbook tanzuEnableSupervisorMonitoring.yml -e VCS=vcs002
    ```

# Post deployment

## Create TKG cluster

Login to Ansible host VM and complete /opt/dhcConfig/group_vars/all/tanzuConfig.yml with required data: namespaces, vmclasses (optional) and tkg. You can find an example in this document: chapter "Deploy procedure - Adjust DHC variables".

Next execute createTkgWorkload.yml playbook with required extra vars:

```bash
ansible-playbook createTkgWorkload.yml -e tanzuNamespace=NAMESPACE -e tkgClusterName=CLUSTER
```

This playbook creates required vmClass used by TKG Cluster specification, tanzu namespace with its configuration and TKG cluster itself.
To login to TKG cluster please execute (an ansible host):

```bash
kubectl vsphere login --server=SERVER --insecure-skip-tls-verify --vsphere-username USERNAME --tanzu-kubernetes-cluster-name CLUSTER --tanzu-kubernetes-cluster-namespace NAMESPACE
```

where:

- SERVER - ip or fqdn of the supervisor
- USERNAME - <administrator@vsphere.local> or platformadministrator/networkadministrator AD role member
- CLUSTER - TKG cluster name
- NAMESPACE - namespace where TKG cluster has been deployed
  
## TKG spec - check compatible Kubernetes versions in your environment

You can list Tanzu Kubernetes releases and view the compatibility for each release using following command.
NOTE: kubectl binary must be installed during tanzuMgmtSupervisor or tanzuCmpSupervisor playbook execution.

```bash
kubectl vsphere login --server=SERVER --insecure-skip-tls-verify --vsphere-username USERNAME
kubectl get tanzukubernetesreleases
```

where:

- SERVER - ip or fqdn of the supervisor
- USERNAME - <administrator@vsphere.local> or platformadministrator/networkadministrator AD role member

Example output:

```bash
NAME                                VERSION                          READY   COMPATIBLE   CREATED   UPDATES AVAILABLE
v1.18.15---vmware.1-tkg.1.600e412   1.18.15+vmware.1-tkg.1.600e412   True    True         21h       [1.19.7+vmware.1-tkg.1.fc82c41]
v1.19.7---vmware.1-tkg.1.fc82c41    1.19.7+vmware.1-tkg.1.fc82c41    True    True         21h       [1.20.2+vmware.1-tkg.1.1d4f79a]
v1.20.2---vmware.1-tkg.1.1d4f79a    1.20.2+vmware.1-tkg.1.1d4f79a    True    True         21h
```

The COMPATIBLE column indicates if that Tanzu Kubernetes release is compatible with the current Supervisor.

The same information is also available using kubectl get tkc <tkgs-cluster-name>.
