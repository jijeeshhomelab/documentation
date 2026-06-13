# Ansible Core Configuration

- Table of Contents
{:toc}

## Changelog

| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 05/22/2020 | First version | Przemyslaw Bojczuk |
| 0.2     | 05/31/2020 | Second version | Przemyslaw Bojczuk |
| 0.2.1     | 06/01/2020 | PowerClI update | Przemyslaw Bojczuk |
| 0.2.1     | 18/02/2020 | ANS001 (Virtual Envionment), remove ANS003 update | Piotr Gesikowski |
  
# Source of the system

| VM Template |Comment |
| :---: | ---- |
| [Ubuntu 18.04 DHC.Linux_Template](dhcAnsibleCoreConfiguration.md) | Template release 0.6 |

# Ansible Core VMs

## ans001

The Ansible Core VM with Python3 in Virtual Environment and Ansible 2.10.4, utilized during the management phase.

## ans002

The Ansible Core VM, so called *the prerequisite VM* (or *the prereq*), utilized during the deployment phase. This VM is removed after the deployment.  

# Automation account

The VCS default automation system level account, which performs deployment:

*group/vars_all*:

```yaml
temporaryCredentials:
  linuxUsername: "next"
```
  
# Disk, LVM Layout and Mount Points

## ans001 - Ansible Core Management VM  
  
On ans001 all the data and binaries are located on the same resource linked to `/opt/dhc`:  

```shell
lrwxrwxrwx 1 next next 9 Mar 26 16:31 /opt/dhc -> /data/dhc
```
  
__Paths:__  

| Path | Ownership | Purpose |
| ---- | ---- | ---- |
| `/opt/dhc/manage`| `next:next`| Playbooks and roles utilized during the production phase. |
| `/opt/binaries` |  `next:next` | Binaries used diring the deployment phase. |
| `/data` |  symlink | All the VCS resources are located here on *ans001* (contrary to *ans002*), however within scripting you should rather rely on `/opt/dhc` and `/opt/binaries` paths. |
| `/var/log/dhcLog` | `next:next` | VCS specific logs. If there are services customized for VCS their logs should be placed here. |

__LVM Layout__

```shell
NAME                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                     8:0    0   60G  0 disk 
├─sda1                  8:1    0    1M  0 part 
├─sda2                  8:2    0    1G  0 part /boot
└─sda3                  8:3    0   59G  0 part 
  ├─VG0-rootLV        253:0    0   10G  0 lvm  /
  ├─VG0-swapLV        253:1    0    4G  0 lvm  [SWAP]
  ├─VG0-optLV         253:3    0    6G  0 lvm  /opt
  ├─VG0-tmpLV         253:4    0    3G  0 lvm  /var/tmp
  ├─VG0-homeLV        253:5    0    8G  0 lvm  /home
  ├─VG0-varLV         253:6    0    7G  0 lvm  /var
  ├─VG0-srvLV         253:7    0    7G  0 lvm  /srv
  ├─VG0-varlogLV      253:8    0  2.5G  0 lvm  /var/log
  └─VG0-varlogauditLV 253:9    0  1.5G  0 lvm  /var/log/audit
sdb                     8:16   0  100G  0 disk 
└─VG1-dataLV          253:2    0   99G  0 lvm  /data
sr0                    11:0    1 1024M  0 rom 
```  
  
__Mount points__

```shell
/dev/mapper/VG0-rootLV        ext4         9.8G  3.8G  5.6G  41% /
/dev/sda2                     ext4         976M  210M  700M  24% /boot
/dev/mapper/VG0-optLV         ext4         5.9G  176M  5.4G   4% /opt
/dev/mapper/VG1-dataLV        ext4          97G   71G   22G  77% /data
/dev/mapper/VG0-varLV         ext4         6.9G  937M  5.6G  15% /var
/dev/mapper/VG0-tmpLV         ext4         2.9G   27M  2.7G   1% /tmp
/dev/mapper/VG0-tmpLV         ext4         2.9G   27M  2.7G   1% /var/tmp
/dev/mapper/VG0-varlogLV      ext4         2.4G  483M  1.8G  21% /var/log
/dev/mapper/VG0-srvLV         ext4         6.9G   37M  6.5G   1% /srv
/dev/mapper/VG0-homeLV        ext4         7.9G  2.0G  5.6G  27% /home
/dev/mapper/VG0-varlogauditLV ext4         1.5G   20M  1.4G   2% /var/log/audit
```

## ans002 - Ansible Core Deployment VM (Prerequisite VM)

The configuration of the partitions and LVM on ans002 is performed during the execution of `preparePrerequisiteVm.configureIps` Code Stream pipeline.
Setup of /dev/sdb on ans002 is inconsistent. As the VM is removed after the deployment, the setup is currently left as is.

__Paths:__

| Path | Ownership | Purpose |
| ---- | ---- | ---- |
| `/opt/dhc/deploy`| `next:next` | Playbooks and roles utilized during the deployment. |
| `/opt/binaries` |  `next:next` | Binaries used diring the deployment phase. |
| `/var/log/dhcLog` | `next:next` | VCS specific logs. If there are services customized for VCS their logs should be placed here.|

__LVM Layout__

```shell
NAME                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                     8:0    0   60G  0 disk 
├─sda1                  8:1    0    1M  0 part 
├─sda2                  8:2    0    1G  0 part /boot
└─sda3                  8:3    0   59G  0 part 
  ├─VG0-rootLV        253:0    0   10G  0 lvm  /
  ├─VG0-optLV         253:1    0    6G  0 lvm  /opt
  ├─VG0-tmpLV         253:2    0    3G  0 lvm  /var/tmp
  ├─VG0-homeLV        253:3    0    1G  0 lvm  /home
  ├─VG0-varLV         253:4    0    7G  0 lvm  /var
  ├─VG0-srvLV         253:5    0    7G  0 lvm  /srv
  ├─VG0-swapLV        253:6    0    4G  0 lvm  [SWAP]
  ├─VG0-varlogLV      253:7    0  2.5G  0 lvm  /var/log
  └─VG0-varlogauditLV 253:8    0  1.5G  0 lvm  /var/log/audit
sdb                     8:16   0  100G  0 disk /opt/binaries
```

Watch out for the /dev/sdb mounting. Which is the result of the execution of code by the Code Stream pipeline's task:

```shell
sudo parted -s /dev/sdb
sudo mkfs.ext4 /dev/sdb
sudo mkdir -p /opt/binaries
sudo mkdir -p /var/log/dhcLog
sudo bash -c 'echo /dev/sdb /opt/binaries ext4 defaults 0 0 >> /etc/fstab'
```
  
__Mount points__

```shell
/dev/mapper/VG0-rootLV        ext4         9.8G  3.0G  6.3G  33% /
/dev/sda2                     ext4         976M  212M  698M  24% /boot
/dev/mapper/VG0-optLV         ext4         5.9G  226M  5.4G   4% /opt
/dev/sdb                      ext4          98G   37G   57G  40% /opt/binaries
/dev/mapper/VG0-srvLV         ext4         6.9G   37M  6.5G   1% /srv
/dev/mapper/VG0-tmpLV         ext4         2.9G   12M  2.8G   1% /tmp
/dev/mapper/VG0-homeLV        ext4         976M  780M  130M  86% /home
/dev/mapper/VG0-varLV         ext4         6.9G  339M  6.2G   6% /var
/dev/mapper/VG0-tmpLV         ext4         2.9G   12M  2.8G   1% /var/tmp
/dev/mapper/VG0-varlogLV      ext4         2.4G  458M  1.9G  20% /var/log
/dev/mapper/VG0-varlogauditLV ext4         1.5G  3.3M  1.4G   1% /var/log/audit

```

# Software

## Compiler

The following compiler packages are installed as requirement for [Pip](https://packaging.python.org/key_projects/#pip).
  
As the compiler presence creates security concerns the access to  ansible core hosts is restricted to the `rsce-< locationCode >-svr-l-admins` group members.  

```shell
g++
g++-7
gcc
gcc-7
gcc-7-base
gcc-8-base
```

## PowerCLI

While PowerShell is available system-wide PowerCLI is installed only within the deployment account `temporaryCredentials.linuxUsername` context.
  
## Python

### Version

VCS Uses Python2 as the engine for Ansible currently. Refer to [Ansible package](#ansible-package) for the constraint information.

### Virtual Environment

The usage of [Virtual Environment](https://packaging.python.org/glossary/#term-virtual-environment) was not considered when creating the original pipelines.  
Currently VCS lives with this debt. The usage of Virtual Environments is planned  to be introduced with next VCS releases.  
  
### Apt

List of the packages installed by Apt.

```shell
python-pip 
python-pip-whl
python-dev
```

### Ansible package

The Source of the Ansible package for Ubuntu 18.04 in VCS is the deb package from the PPA repository

- <https://launchpad.net/~ansible/+archive/ubuntu/ansible-2.8>

PPA repository provides only one upstream version of the ansible package. As a result of the bug related to OVA import, the ansible deb package is currently installed  
from /opt/binaries directory.  
  
PPA Ansible 2.8 package is built with Python2 components as the dependency.
  
The ansible package info:

```shell
ansible 2.8.5-1ppa~bionic Ansible IT Automation
```

sha256 checksum:

```shell
d1a6b8578b09c3366772b5559e5e148cbe5484ea7389e102e6d04a597f73efc0  /opt/binaries/apt/archives/ansible_2.8.5-1ppa_bionic_all.deb
```

sha1 checksum:

```shell
380d6300b92c9efe9480b092b02fd784838b0508  /opt/binaries/apt/archives/ansible_2.8.5-1ppa_bionic_all.deb
```

md5 checksum:

```shell
e8fafccd099b73b5840dcec62899c7c8  /opt/binaries/apt/archives/ansible_2.8.5-1ppa_bionic_all.deb
```

### Pip

Pip Installs Packages (PIP) provides extra python modules.

List of the packages installed by PIP:  

| __Module name__ | __Requirement__ | __Comments__ |
| --- | --- | --- |
| boto |   |    |
| cachetools |   |    |
| certifi |   |    |
| cffi |   |    |
| chardet |   |    |
| cryptography |   |    |
| enum34 |   |    |
| google-auth |   |    |
| idna |   |    |
| infoblox |   |    |
| ipaddr |   |    |
| ipaddress |   |    |
| jmespath |   |    |
| lxml |   |    |
| netaddr |   |    |
| ntlm-auth |   |    |
| pyOpenSSL |   |    |
| pyasn1 |   |    |
| pyasn1-modules |   |    |
| pycparser |   |    |
| pypsexec |   |    |
| python-ldap |   |    |
| pyvmomi |   |    |
| pywinrm |   |    |
| requests |   |    |
| requests-ntlm |   |    |
| rsa |   |    |
| setuptools |   |    |
| six |   |    |
| smbprotocol |   |    |
| urllib3 |   |    |
| xmltodict |   |    |

## Non-Python Packages

| __Package name__ | __Requirement__ | __Comments__ |
| --- | --- | --- |
| libsasl2-dev |   | source: apt |
| libldap2-dev |   | source: apt  |
| libssl-dev |   |  source: apt |
| jq |   | source: apt  |
| libjq1 |   | source: apt  |
| libonig4 |   |  source: apt |
| unzip |   | source: apt  |
| powershell | Required by VMWare's PowerCLI | source: `https://packages.microsoft.com/config/ubuntu/18.04/`  |
| PowerCLI | | source: role `dhc-installPowerCli` |
| chromium-browser |   | source: apt  |
| chromium-chromedriver |   | source: apt  |
| python3-selenium |   | source: apt  |

### Installation

### ans001 Installation

The automated configuration is carried out during the execution of the ansible playbook:  
`deploy/createAnsibleCoreInVenv.yml`

### ans002 Installation

The automated configuration is carried out during the execution of the Code Stream Pipeline. Please find in the [Appendix](#appendix) the set of configuring commands.
  
# Appendix
  
## Prerequisite VM Pipeline System Level Configuration

```bash
set -x
sudo parted -s /dev/sdb
sudo mkfs.ext4 /dev/sdb
sudo mkdir -p /opt/binaries
sudo mkdir -p /var/log/dhcLog
sudo bash -c 'echo /dev/sdb /opt/binaries ext4 defaults 0 0 >> /etc/fstab'
sudo mount /opt/binaries
sudo mkdir -p /opt/binaries/pipModules
sudo mkdir -p /opt/binaries/apt/archives
sudo chmod -R 755 /opt/binaries
sudo chown -R next:next /opt/binaries
sudo chown -R next:next /var/log/dhcLog

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0x6125E2A8C77F2818FB7BD15B93C4A3FD7BB9C367
wget --no-check-certificate https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -P /opt/binaries/apt/archives/
wget --no-check-certificate https://storage.googleapis.com/dpcnext/buildbinaries/$ansibleDeb -P /opt/binaries/apt/archives/
sudo dpkg -i /opt/binaries/apt/archives/packages-microsoft-prod.deb

#sudo add-apt-repository ppa:ansible/ansible-2.8 -y
sudo apt clean all
sudo apt update
sudo apt upgrade --download-only -y
sudo apt install --download-only -y python-pip python-pip-whl libsasl2-dev python-dev libldap2-dev libssl-dev jq libjq1 libonig4 unzip powershell chromium-browser chromium-chromedriver python3-selenium
sudo cp -r /var/cache/apt /opt/binaries
sudo chmod -R 755 /opt/binaries
sudo chown -R next:next /opt/binaries

sudo apt update
sudo DEBIAN_FRONTEND=noninteractive apt upgrade -y
sudo DEBIAN_FRONTEND=noninteractive apt-get install -y /opt/binaries/apt/archives/$ansibleDeb
sudo DEBIAN_FRONTEND=noninteractive apt install -y python-pip python-pip-whl libsasl2-dev python-dev libldap2-dev libssl-dev

pip download pyvmomi pypsexec pywinrm netaddr ipaddr python-ldap lxml jmespath google-auth boto requests infoblox infoblox-client pandas openpyxl pyOpenSSL pexpect -d /opt/binaries/pipModules/
sudo pip install requests google-auth boto ipaddr netaddr
sudo chmod -R 755 /opt/binaries
sudo chown -R next:next /opt/binaries

git clone --depth 1 -b $gitbranch https://dl-eso-cs-private-pl:$accessToken@git.atosone.com/private-cloud/dhc.git /opt/dhc
sudo rm /etc/netplan/99-netcfg-vmware.yaml
cd /opt/dhc/deploy/
git config --unset-all remote.origin.url
mkdir -p /opt/dhc/docs
sudo chmod -R 755 /opt/dhc/docs
sudo chown -R next:next /opt/dhc/docs
mkdir -p /opt/dhc/deploy/group_vars
sudo chmod -R 755 /opt/dhc/deploy/group_vars
sudo chown -R next:next /opt/dhc/deploy/group_vars
cd /opt/dhc/deploy/
ansible-playbook createGroupVars.yml --extra-vars '{"input":{"networkMgmtPgName":"${input.networkMgmtPgName}","networkVxlanPgName":"${input.networkVxlanPgName}","networkVrealizePgName":"${input.networkVrealizePgName}","vcs002NetworkMgmtCidr":"${input.vcs002NetworkMgmtCidr}","vcs001NetworkMgmtCidr":"${input.vcs001NetworkMgmtCidr}","vcs002Octet":"${input.vcs002Octet}","vcs001Octet":"${input.vcs001Octet}","temporaryPassword":"${input.temporaryPassword}","backupAmountofCustomerVms":"${input.backupAmountofCustomerVms}","backupAvamarServerFqdn":"${input.backupAvamarServerFqdn}","backupAvamarServerIP":"${input.backupAvamarServerIP}","backupDataDomainFqdn":"${input.backupDataDomainFqdn}","backupDataDomainIP":"${input.backupDataDomainIP}","backupEnableAvamarBackupofCustomerVms":"${input.backupEnableAvamarBackupofCustomerVms}","snowInstanceUrl":"${input.snowInstanceUrl}","snowUser":"${input.snowUser}","snowUserPassword":"${input.snowUserPassword}","snowEvaniosArangoDb":"${input.snowEvaniosArangoDb}","customerCode":"${input.customerCode}","locationCode":"${input.locationCode}","numberOfComputeHostsInWorkloadDomain":"${input.numberOfComputeHostsInWorkloadDomain}","computeHostsStartCidr":"${input.computeHostsStartCidr}","numberOfManagementHosts":"${input.numberOfManagementHosts}","managementHostsStartCidr":"${input.managementHostsStartCidr}","drType":"${input.drType}","vmwareUserPassword":"${input.vmwareUserPassword}","vmwareUser":"${input.vmwareUser}","deepSecurityLinuxPolicyId":"${input.deepSecurityLinuxPolicyId}","deepSecurityWindowsPolicyId":"${input.deepSecurityWindowsPolicyId}","deepSecurityToken":"${input.deepSecurityToken}","deepSecurityTenantId":"${input.deepSecurityTenantId}","networkMgmtCidr":"${input.networkMgmtCidr}","networkMgmtGateway":"${input.networkMgmtGateway}","networkMgmtVlan":"${input.networkMgmtVlan}","networkMgmtNetmask":"${input.networkMgmtNetmask}","networkVmotionCidr":"${input.networkVmotionCidr}","networkVmotionVlan":"${input.networkVmotionVlan}","networkVsanCidr":"${input.networkVsanCidr}","networkVsanVlan":"${input.networkVsanVlan}","networkVxlanCidr":"${input.networkVxlanCidr}","networkVxlanVlan":"${input.networkVxlanVlan}","networkVrealizeCidr":"${input.networkVrealizeCidr}","networkVrealizeVlan":"${input.networkVrealizeVlan}","networkEdgeCidr":"${input.networkEdgeCidr}","networkEdgeGateway":"${input.networkEdgeGateway}","networkEdgeNetmask":"${input.networkEdgeNetmask}","networkEdgeVlan":"${input.networkEdgeVlan}","networkEdgeRangeStart":"${input.networkEdgeRangeStart}","networkEdgeRangeEnd":"${input.networkEdgeRangeEnd}","dpcDomainPrefix":"${input.dpcDomainPrefix}","enableWsusAutoPatchApproval":"${input.enableWsusAutoPatchApproval}","nsxtLicense":"${input.nsxtLicense}","vcsComputeLicense":"${input.vcsComputeLicense}","vsanComputeLicense":"${input.vsanComputeLicense}","vrliLicenseKeyForCmpCluster":"${input.vrliLicenseKeyForCmpCluster}","casAuthToken":"${input.casAuthToken}","vropsLicense":"${input.vropsLicense}","InternetAccess":"${input.InternetAccess}","externalProxyIp":"${input.externalProxyIp}","externalProxyPort":"${input.externalProxyPort}","externalProxyAuthMethod":"${input.externalProxyAuthMethod}","externalProxyLogin":"${input.externalProxyLogin}","externalProxyPassword":"${input.externalProxyPassword}","vidmLicense":"${input.vidmLicense}","locationCodeDr":"${input.locationCodeDr}","vsanEncryption":"${input.vsanEncryption}","esxiLicense":"${input.esxiLicense>","vsanWitnessName":"${input.vsanWitnessName}","vsanWitnessMgmtIpAddress":"${input.vsanWitnessMgmtIpAddress>","vsanWitnessVsanIpAddress":"${input.vsanWitnessVsanIpAddress}","vsanWitnessNetworkIpAddress":"${input.vsanWitnessNetworkIpAddress}","vsanWitnessNetmask":"${input.vsanWitnessNetmask}","vsanWitnessGateway":"${input.vsanWitnessGateway}","vsanWitnessSize":"${input.vsanWitnessSize}","vsanWitnessMgmtNetworkCidr":"${input.vsanWitnessMgmtNetworkCidr}","vCenterDataCenter":"${input.vCenterDataCenter}","vCenterCluster":"${input.vCenterCluster}","vCenterDatastore":"${input.vCenterDatastore}","vCenterResourcePool":"${input.vCenterResourcePool}","vCenterVmFolder":"${input.vCenterVmFolder}","vCenterUser":"${input.vCenterUser}","infobloxLicense":"${input.infobloxLicense}","vrniLicense":"${input.vrniLicense}">'
ansible-playbook createNsxVars.yml --extra-vars '{"input":{"temporaryPassword":"${input.temporaryPassword}","defaultBuildExec":"${input.nsxEnableDefaultLogicalSwitchesBuild}","bgpOrStatic":"${input.nsxBgpOrStatic}","uplinkIp":"${input.nsxT0uplinkIp}","uplinkSpl":"${input.nsxT0uplinkSubnetPrefixLenght}","bgpAsNumber":"${input.nsxBgpAsNumber}","neighborName":"${input.nsxNeighborName}","neighborIp":"${input.nsxNeighborIp}","neighborAsNumber":"${input.nsxNeighborAsNumber}","staticNetwork":"${input.nsxStaticRouteNetwork}","staticName":"${input.nsxStaticRouteName}","staticDescription":"${input.nsxStaticRouteDescription}","staticNextHopAddress":"${input.nsxStaticRouteNextHopAddress}","nsxT0UplinkVlan":"${input.nsxT0UplinkVlan}">'
ansible-playbook configureAnsiblePrerequisiteVm.yml
ansible-playbook createPrerequisiteVmEmailInventory.yml
sleep 5

```
