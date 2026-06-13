# centralDebRepositoryUsage

## Changelog

| Version | Date | Author | Changes |
|---------|------|------|---------|
| 0.1 | 10/12/2024 | Adrian Giurgiu | New version |

## Introduction

Design and Workflow of the Internal DEB Repository System
The internal DEB repository, hosted on DEB001, serves as the cornerstone for managing software packages, patches, and updates within the Ubuntu environment. The system leverages a combination of Apache2 and Aptly to efficiently mirror, snapshot, and publish DEB packages, ensuring seamless software delivery across the infrastructure.

Key Components and Architecture
Apache2 Web Server:
The repository is made accessible to all managed hosts through an Apache2 web server. This ensures the DEB packages, snapshots, and mirrors are readily downloadable over HTTP(S).

Aptly Service:

Mirroring: Aptly mirrors packages from official Ubuntu repositories or upstream DEB sources, ensuring the availability of required software for patching and updates.
Snapshot Creation: After mirroring, snapshots of the repositories are generated. These snapshots provide a stable and consistent state of the repository, isolating changes and ensuring controlled rollouts.
Publishing: The snapshots are published via the Apache2 service, making them available to all target hosts.
Configuration Distribution:
Managed Ubuntu hosts are configured to utilize the internal repository through the /etc/apt/sources.list file. This file is dynamically updated during the patching process to redirect package retrieval to DEB001.

Ansible Core Management Host (ANS001):
Patching and configuration changes are orchestrated from ANS001, which ensures all VMs update their sources.list to point to the internal repository. This centralized approach streamlines the patching workflow and minimizes manual intervention.

Workflow Overview
Mirror Creation: Aptly mirrors desired repositories (from Ubuntu or upstream DEB sources) and stores them within DEB001.
Snapshot Generation: Snapshots of the mirrors are created periodically or before significant updates.
Publishing: The snapshots are published to a specific endpoint served by Apache2.
VM Configuration: Target Ubuntu hosts are updated with the repository location via their /etc/apt/sources.list file, ensuring they pull updates and patches from the internal repository.
Patching Execution: Using Ansible Core on ANS001, patches are applied across all managed hosts, leveraging the stable snapshots provided by DEB001.
Benefits of the Design
Consistency: Snapshots ensure hosts retrieve packages from a stable and unchanging repository state.
Reliability: Hosting the repository internally mitigates external dependency risks, ensuring updates are always available.
Centralized Management: Leveraging Ansible Core simplifies repository management and patching across multiple VMs.
Scalability: The system can scale with the environment, supporting more mirrors or additional hosts as needed.
This robust design ensures that the internal DEB repository is a reliable and efficient mechanism for managing software updates within the Ubuntu infrastructure.

### Purpose

Create Deb repository using ubuntu mirrors / upstream ( central Deb server )

### Audience

- VCS Engineering
- VCS Operations

### Scope

This instruction covers:

- deployment

## Prerequisites

### General Information

If we have a central deb repository already configured then proceed the deployment with the extra parameters shown below. This approach will create a variable in groupvars ( if empty, will go with the default configuration, if not it will go with custom configuration )

```bash
ansible-playbook createDebRepository.yml -e "centralDebIp=IP or FQDN"
```

If we go with the default deb repository design ( as previous VCS versions ) then proceed with the default run.

```bash
ansible-playbook createDebRepository.yml
```

### Working Directory

All the playbooks must be executed from the `/opt/dhc/deploy` directory.

To navigate there execute:

```bash
cd /opt/dhc/deploy
```

To check which directory you are in execute:

```bash
pwd
```

### sources.list and Aptly Snapshots

The default configuration of *DEB001* provides automated snapshot creation. The snapshots are created and published 7th day of each month, which aligns with Microsoft's process of issuing patches each Tuesday after the second weekend of each Month and allows to synchronize VCS patching of Windows and Ubuntu Linux hosts during the same period.

Whenever snapshot is created the `sources.list` file is created with the appropriate information and the ongoing sources list is moved to `sources.list-< releaseDate >`.

`sources.list` files are publish in `https://< deb001 >/archive` directory and provided automatically to the target hosts when patching is executed.

To check current source.list on the target host execute:

```bash
cat /etc/apt/sources.list
```

To check currently available sources.list navigate with the web browser to `https://< deb001 >/archive/sources.list`

__Note:__ The initial snapshot can take up to 16 hours (up to 8 hours to synchronize deb packages and up to 8 hours to generate the local metadata for each package and publishing it). Which means that even though *deb001* host has just been built it can be not ready to serve patches immediately.

#### Configuring cronjobs

Deb repository server contains cronjob for Mirror creation, snapshot generation and snapshot publishing on apache2 which are performed according to the design and ubuntu best practices.

### sources.list Configuration

Whenever patching is executed the role `dhc-configureSourcesList` is executed. The automated patching uses `https://< deb001 >/archive/sources.list` with the latest snapshot. The repository on targets is always refreshed. Additionally, the role allows to configure original *sources.list* should it be needed. For more information and usage examples please find `roles/dhc-configureSourcesList/README.md`
