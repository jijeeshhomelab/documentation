# Linux Template Configuration

- Table of Contents
{:toc}

# Changelog

|    Date    |   TOS   |   Issue   | Author | Description |
|------------|---------|-----------|--------|-------------|
| 25.09.2019| N/A | N/A | Przemyslaw Bojczuk | First version |
| 03.04.2019 | N/A | N/A | Przemyslaw Bojczuk | Release 0.7 updates |
| 11.09.2019 | N/A | N/A | Przemyslaw Bojczuk | Release 0.8 updates |
| 11.05.2023 | TBC | VCS-9451 | Lukasz Bienkowski | Change to Atos Global Image, introduce Ubuntu 22, document VCS template build process |
| 27.12.2023 | TBC | VCS-11653 | Lukasz Tomaszewski | Minor updates |
| 27.12.2023 | TBC | VCS-11651 | Lukasz Tomaszewski | Security/compliance updates |
| 14.08.2024 | TBC | VCS-13572 | Lukasz Tomaszewski | Minor updates (unattended upgrades, apt upgrade) |
| 04.06.2025 | VCS-2.0 | VCS-16218 | Lukasz Bienkowski | Renew password for user next, disable cloud-init, adjust /etc/DHC-release |

# Latest release

__Note:__ for the info release always refer to:  

```shell
/etc/DHC-release
```
  
Ver: 1.4
Update date: 04/06/2025
Update timestamp: 1749031156

Checksums:
MD5(DHC.Next_Linux_U22.ovf)= c056006501d94c3dab7b7d7c65a6f3b9
MD5(DHC.Next_Linux_U22-1.vmdk)= 6acbb0142e724fd04edb9398c4b5a928
MD5(DHC.Next_Linux_U22-2.nvram)= 0e55c5eb15ff49937b728e3601b591a9

# 1. Template requirements

| Resource | Value | Comment|
| :---: | ---- | ---- |
| CPU | 1 core| the value can be altered during the deployment |
| RAM | 2GB | the value can be altered during the deployment |
| NIC |  1 | aliased ens160 (alt-name enp3s0) |
| Storage| 1 disk, 60 GB | for partitions and LVM |

# 2. Source of installation

The VCS template is prepared with image released by Global Product Practice Server OS Team (GPP). It is based on Ubuntu 22.02.2 LTS release with Atos version 1.0 compliant with TSS 9.0 standard security measures.

The technical specification of the image is stored here: [Technical Security Specification for Server Unix](https://atos365.sharepoint.com/sites/100000120/PublishedStorage/Technical%20Security%20Specifications%20for%20Server%20Unix.pdf)

## 2.1 Accessing GPP releases

Global Product Practice Server OS Team releases are available on Atos Sharepoint space, where an access has to be requested. This team delivers golden images for Linux, Unix, VMware and Windows. Access can be granted by filling a request, which pops up after first access to image repository link. The Sharepoint is available here: [Atos Global Images](https://atos365.sharepoint.com/sites/100004478/Shared%20Documents/Forms/AllItems.aspx?FolderCTID=0x012000BDA4B5254E3BE24EA0608AC8F542DEBD&id=%2Fsites%2F100004478%2FShared%20Documents%2FAtos_Global_Images&viewid=75026d5d-83b5-4a42-8388-b0b803c08623)

Passwords and accounts for golden images are stored in dedicated password vaults handled by GPP.  
The access can be requested using the following access form: [Key Vault Access Request Form](https://forms.office.com/pages/responsepage.aspx?id=xg9EM8e3LEG7cw5wsBmNWr2SaycS1tVKlT1uGRrAVPVUNTVUUVVWVlJETFA2UUpGWVpHSjdIT1ZYVy4u)

The procedure of getting an access to GPP Key Vault is described here: [GPP Key Vault Access Procedure](https://atos365.sharepoint.com/:w:/r/sites/DCHGlobal-DataCentersandHosting/Portfolio/Server/Global%20Product%20Practice%20Server%20OS%20x86%20%26%20UNIX/ASSESSMENTS/GPPKeyvault/GPP%20Key%20Vault%20Access%20Procedure-v1.0.doc?d=wc00792fdc691404297e3aec772ba7bd0&csf=1&web=1&e=2OqR4i)

It is possible to subscribe to GPP updates by requesting a membership to the dedicated distribution list.
The membership can be requested here: [GPP Distribution List Membership Request](https://forms.office.com/pages/responsepage.aspx?id=xg9EM8e3LEG7cw5wsBmNWqFyMpNwcQ5AjCkxr4H7UNdUOTY3TVVEVTlNUDU2VDVJVjJLNzFYV1M1Mi4u)

For any questions related to GPP activity please contact the team via mail: [gpp-server-os@atos.net](gpp-server-os@atos.net)

# 3. Partition and LVM setup

```shell
sda                     8:0    0   60G  0 disk  
├─sda1                  8:1    0  512M  0 part /boot/efi  
├─sda2                  8:2    0    2G  0 part /boot  
└─sda3                  8:3    0   57.5G  0 part  
  ├─Ubuntu_vg0-rootlv        253:0    0    5G  0 lvm  /  
  ├─Ubuntu_vg0-varlv         253:1    0    5G  0 lvm  /var 
  ├─Ubuntu_vg0-optlv         253:2    0    6G  0 lvm  /opt  
  ├─Ubuntu_vg0-homelv        253:3    0    2G  0 lvm  /home  
  ├─Ubuntu_vg0-usrlv         253:4    0    5G  0 lvm  /usr  
  ├─Ubuntu_vg0-tmplv         253:5    0    2G  0 lvm  /tmp
  ├─Ubuntu_vg0-swaplv        253:5    0    4G  0 lvm  [SWAP]  
  ├─Ubuntu_vg0-varloglv      253:7    0    3G  0 lvm  /var/log  
  └─Ubuntu_vg0-varlogauditlv 253:8    0    2G  0 lvm  /var/log/audit  
```

# 4. Local accounts and groups

| Login | *id* output | Comments |
| :---: | ---- | ---- |
| next | uid=1000(next) gid=1000(next) groups=1000(next),1002(allowssh),1003(seelogs),1004(atosadm) | local automation account |
| root | uid=0(root) gid=0(root) groups=0(root) | root account is locked - <https://help.ubuntu.com/community/RootSudo> ; no virtual console and no ssh login are possible ; if a regular user account is allowed it can increase privileges via *sudo -i* or, which is the most recommended option, execute commands via: *sudo < command >*|

# 5. VCS image build process

Atos Global Image requires a customization to be used in VCS in terms of accounts, partitions etc. The building of VCS image is described below.

## 5.1 Atos Image installation

There is an installation guide provided by GPP with all initial requirements and as well accounts which are used in the golden image.
The guide is available here: [Build Installation Guide - Ubuntu 22.04](https://atos365.sharepoint.com/:b:/r/sites/100000409/DCH%20Techartifacts/Build%20Installation%20Guide%20Ubuntu%2022.04.pdf?csf=1&web=1&e=GcXfgJ)

## 5.2 VCS customization steps

After a successful installation of provided ISO following steps have to be executed in order of appearance.

__Note:__ All actions below have to be performed using atosadm account unless stated otherwise.

### 5.2.1 Hostname adjustment

```shell
sudo hostnamectl set-hostname dhclinux-u22
sudo sed -i "s/ubuntu22/dhclinux-u22/g" /etc/hosts
```

Machine can be rebooted to have the new hostname visible in bash prompt.

__Note:__ After execution of sed commands there might be an information about temporary name resolution failure

### 5.2.2 Networking preparation

All initial netplan yaml files have to be removed:

```shell
sudo rm /etc/netplan/*
```

Temporary networking has to be set for downloading audit package and for SSH access to ease command execution

```shell
sudo vim /etc/netplan/99-temp.yaml
```

Example netplan configuration:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens160:
      dhcp4: no
      dhcp6: no
      addresses:
        - x.x.x.x/x
      routes:
        - to: default
          via: x.x.x.x
```

__Note:__ In comparison to Ubuntu 18.04 a configuration of default gateway using gateway4 is not supported anymore. Default network needs to be implemented using route/default syntax as above.

After netplan file preparation there is a need to activate it:

```shell
sudo netplan generate
sudo netplan apply
```

Enable and start resolved service:

```shell
sudo systemctl enable systemd-resolved.service
sudo systemctl start systemd-resolved.service
```

### 5.2.3 Compliancy adjustments

Compliancy scripts are using ss with different path (than ubuntu). Create symbolic link:

```shell
sudo ln -s /usr/bin/ss /usr/sbin/ss
```

### 5.2.4 Enable password authentication via SSH

In Atos golden image a password authentication using SSH is disabled, so it has to be enabled again

```shell
sudo sed -i "s/PasswordAuthentication no/PasswordAuthentication yes/g" /etc/ssh/sshd_config
sudo systemctl restart sshd.service
```

### 5.2.5 Enable loading of bashrc in SSH sessions for new accounts

bashrc is only active using local console, it has to be enabled as well for SSH

```shell
sudo bash -c "echo -e 'if [ -f ~/.bashrc ]; then . ~/.bashrc\nfi' >> /etc/skel/.bash_profile"
```

### 5.2.6 Change umask value

Because of umask settings become too strict, there is a need to adjust this to 022 to meet virtual environment implementation requirements.

```shell
sudo sed -i 's/umask 027/umask 022/g' /etc/profile.d/umask.sh
sudo sed -i 's/umask 027/umask 022/g' /etc/skel/.bashrc
sudo sed -i 's/umask 027/umask 022/g' /etc/skel/.bash_profile
 
sudo sed -i 's/umask 077/umask 022/g' /root/.bash_profile
sudo sed -i 's/umask 077/umask 022/g' /root/.bashrc
sudo sed -i 's/umask 077/umask 022/g' /root/.cshrc
sudo sed -i 's/umask 077/umask 022/g' /root/.tcshr
```

### 5.2.7 Adding VCS user and perform cleanup

There is a need to create user "next" and clean up unnecessary accounts:

```shell
sudo adduser next
sudo usermod -aG atosadm next
sudo usermod -aG allowssh next
sudo usermod -aG seelogs next
```

```shell
sudo userdel -r nagios
sudo rm /home/nagios
```

### 5.2.8 Prepare LVM and prepare /var/log

There is a need to adjust existing LVM design to VCS needs:

- to extend /opt

```shell
sudo lvextend -rL+3G /dev/Ubuntu_vg0/optlv
```

- make backup of log directory

```shell
mkdir ~/log_bkp
sudo rsync -avz /var/log ~/log_bkp
```

- create lvm for log and audit log

```shell
sudo lvcreate -L 3G -n varloglv Ubuntu_vg0
sudo mkfs.xfs /dev/Ubuntu_vg0/varloglv
sudo lvcreate -L 2G -n varlogauditlv Ubuntu_vg0
sudo mkfs.xfs /dev/Ubuntu_vg0/varlogauditlv
```

__Note:__ Filesystem in Ubuntu 22.04 is now XFS instead of EXT4 as in previous VCS templates

- inject LVM into fstab
  
```shell
sudo bash -c "echo '/dev/mapper/Ubuntu_vg0-varloglv /var/log xfs defaults 0 1' >> /etc/fstab"
sudo bash -c "echo '/dev/mapper/Ubuntu_vg0-varlogauditlv /var/log/audit xfs defaults 0 1' >> /etc/fstab"
```

- mount new LVM and copy backed up log files

```shell
sudo mount -a
sudo rsync -avz ~/log_bkp/log/ /var/log
```

- create the dedicated audit and dhcLog directory with proper permissions

```shell
sudo mkdir /var/log/audit
sudo mkdir /var/log/dhcLog
sudo chown next:next /var/log/dhcLog/
```

### 5.2.9 Install audit package

```shell
sudo apt update
sudo apt upgrade
sudo apt install -y auditd
```

__Note:__ if proxy is required for APT it has to be adjusted by adding a conf file to /etc/apt/apt.conf.d/ with following settings:

```shell
Acquire::http::Proxy "http://x.x.x.x:xxxx/";
Acquire::https::Proxy "http://x.x.x.x:xxxx/";
```

or via environmental export commands

__Note:__ Remove proxy or environmental variables after successful installation

### 5.2.10 Configure audit

Configuration has in /etc/audit/auditd.conf has to be replaced by the following:

```sh
log_file = /var/log/audit/audit.log
log_format = RAW
log_group = adm
priority_boost = 4
flush = INCREMENTAL
freq = 20
num_logs = 5
disp_qos = lossy
dispatcher = /sbin/audispd
name_format = NONE
max_log_file = 6
max_log_file_action = keep_logs
space_left = 80
space_left_action = suspend
##tcp_listen_port = 60
tcp_listen_queue = 5
tcp_max_per_addr = 1
##tcp_client_ports = 1024-65535
tcp_client_max_idle = 0
enable_krb5 = no
krb5_principal = auditd
##krb5_key_file = /etc/audit/audit.key
distribute_network = no
```

### 5.2.11 Configure audit.rules

Audit rules have to be inserted into /etc/audit/rules.d/audit.rules

```sh
# audit.rules file to cover top rules including PCI-DSS v3.1 as linux hardening best practice
# version: 0.1
# date: 07.2019
#

# Remove any existing rules
-D

# Buffer Size
## Feel free to increase this if the machine panic's
-b 8192

# Failure Mode
## Possible values: 0 (silent), 1 (printk, print a failure message), 2 (panic, halt the system)
-f 1

# Ignore errors
## e.g. caused by users or files not found in the local environment  
-i 

## This determine how long to wait in burst of events
--backlog_wait_time 60000

### Rules
#
# 1. Audit logs

-w /var/log/audit/ -k auditlog

# 2. Audit configuration of auditd deamon and tools

-w /etc/audit/ -p wa -k auditconfig
-w /etc/libaudit.conf -p wa -k auditconfig
-w /etc/audisp/ -p wa -k audispconfig

-w /sbin/auditctl -p x -k audittools
-w /sbin/auditd -p x -k audittools

# 3. VMWare tools audit

-a exit,never -F arch=b64 -S fork -F success=0 -F path=/usr/lib/vmware-tools -F subj_type=initrc_t -F exit=-2

## Kernel parameters

-w /etc/sysctl.conf -p wa -k sysctl

## 4. Kernel module loading and unloading

-a always,exit -F perm=x -F auid!=-1 -F path=/sbin/insmod -k modules
-a always,exit -F perm=x -F auid!=-1 -F path=/sbin/modprobe -k modules
-a always,exit -F perm=x -F auid!=-1 -F path=/sbin/rmmod -k modules
-a always,exit -F arch=b64 -S finit_module -S init_module -S delete_module -F auid!=-1 -k modules

## 5. Modprobe configuration

-w /etc/modprobe.conf -p wa -k modprobe

## 6. Mount operations

-w /etc/fstab -p wa -k mount
-a always,exit -F arch=b64 -S mount -S umount2 -F auid!=-1 -k mount

## 7. Cron config and jobs schedules

-w /etc/cron.allow -p wa -k cron
-w /etc/cron.deny -p wa -k cron
-w /etc/cron.d/ -p wa -k cron
-w /etc/cron.daily/ -p wa -k cron
-w /etc/cron.hourly/ -p wa -k cron
-w /etc/cron.monthly/ -p wa -k cron
-w /etc/cron.weekly/ -p wa -k cron
-w /etc/crontab -p wa -k cron
-w /var/spool/cron/crontabs/ -k cron

## 8. User, group, password databases

-w /etc/group -p wa -k etcgroup
-w /etc/passwd -p wa -k etcpasswd
-w /etc/gshadow -k etcgroup
-w /etc/shadow -k etcpasswd
-w /etc/security/opasswd -k opasswd

## 9. Sudoers file changes

-w /etc/sudoers -p wa -k actions

## 10. Passwd
-w /usr/bin/passwd -p x -k passwd_modification


## 11. Tools to change group identifiers

-w /usr/sbin/groupadd -p x -k group_modification
-w /usr/sbin/groupmod -p x -k group_modification
-w /usr/sbin/addgroup -p x -k group_modification
-w /usr/sbin/useradd -p x -k user_modification
-w /usr/sbin/usermod -p x -k user_modification
-w /usr/sbin/adduser -p x -k user_modification

## 12. Login configuration and information

-w /etc/login.defs -p wa -k login
-w /etc/securetty -p wa -k login
-w /var/log/faillog -p wa -k login
-w /var/log/lastlog -p wa -k login
-w /var/log/tallylog -p wa -k login

### 13. Changes to hostname

-a always,exit -F arch=b64 -S sethostname -S setdomainname -k network_modifications

### 14. Changes to core OS files

-w /etc/hosts -p wa -k network_modifications
-w /etc/sysconfig/network -p wa -k network_modifications
-w /etc/network/ -p wa -k network
-a always,exit -F dir=/etc/NetworkManager/ -F perm=wa -k network_modifications
-w /etc/sysconfig/network -p wa -k network_modifications

### 15. Changes to issue

-w /etc/issue -p wa -k etcissue
-w /etc/issue.net -p wa -k etcissue

## 16. System startup scripts

-w /etc/inittab -p wa -k init
-w /etc/init.d/ -p wa -k init
-w /etc/init/ -p wa -k init

## 17. Pam configuration

-w /etc/pam.d/ -p wa -k pam
-w /etc/security/limits.conf -p wa  -k pam
-w /etc/security/pam_env.conf -p wa -k pam
-w /etc/security/namespace.conf -p wa -k pam
-w /etc/security/namespace.init -p wa -k pam

## 18. SSH configuration

-w /etc/ssh/sshd_config -k sshd

# 19. Systemd

-w /bin/systemctl -p x -k systemd 
-w /etc/systemd/ -p wa -k systemd

## 20. Sudoers process

-w /bin/su -p x -k priv_esc
-w /usr/bin/sudo -p x -k priv_esc
-w /etc/sudoers -p rw -k priv_esc
-w /etc/sudoers.d/ -p rw -k priv_esc

## 21. Power state

-w /sbin/shutdown -p x -k power
-w /sbin/poweroff -p x -k power
-w /sbin/reboot -p x -k power
-w /sbin/halt -p x -k power

# Exploitation rules

## 22. 32bit API exploit

-a always,exit -F arch=b32 -S all -k 32bit_api

## 23. Reconnaissance

-w /usr/bin/whoami -p x -k recon
-w /etc/issue -p r -k recon
-w /etc/hostname -p r -k recon

## 24. Bin suspicious activity

-w /usr/bin/wget -p x -k susp_activity
-w /usr/bin/curl -p x -k susp_activity
-w /usr/bin/base64 -p x -k susp_activity
-w /bin/nc -p x -k susp_activity
-w /bin/netcat -p x -k susp_activity
-w /usr/bin/ncat -p x -k susp_activity
-w /usr/bin/ssh -p x -k susp_activity
-w /usr/bin/socat -p x -k susp_activity
-w /usr/bin/wireshark -p x -k susp_activity
-w /usr/bin/rawshark -p x -k susp_activity
-w /usr/bin/rdesktop -p x -k sbin_susp

## 25. Sbin suspicious activity

-w /sbin/iptables -p x -k sbin_susp 
-w /sbin/ifconfig -p x -k sbin_susp
-w /usr/sbin/tcpdump -p x -k sbin_susp
-w /usr/sbin/traceroute -p x -k sbin_susp

## 26. Injection code detection

-a always,exit -F arch=b32 -S ptrace -k tracing
-a always,exit -F arch=b64 -S ptrace -k tracing
-a always,exit -F arch=b32 -S ptrace -F a0=0x4 -k code_injection
-a always,exit -F arch=b64 -S ptrace -F a0=0x4 -k code_injection
-a always,exit -F arch=b32 -S ptrace -F a0=0x5 -k data_injection
-a always,exit -F arch=b64 -S ptrace -F a0=0x5 -k data_injection
-a always,exit -F arch=b32 -S ptrace -F a0=0x6 -k register_injection
-a always,exit -F arch=b64 -S ptrace -F a0=0x6 -k register_injection

## 27. Privilege Abuse (using power)

-a always,exit -F dir=/home -F uid=0 -F auid>=1000 -F auid!=4294967295 -C auid!=obj_uid -k power_abuse

##  28. Software APT mgmt

-w /usr/bin/apt-add-repository -p x -k software_mgmt
-w /usr/bin/apt-get -p x -k software_mgmt
-w /usr/bin/aptitude -p x -k software_mgmt

## 29. Unauthorized file access

-a always,exit -F arch=b64 -S creat -S open -S openat -S open_by_handle_at -S truncate -S ftruncate -F exit=-EACCES -F auid>=500 -F auid!=4294967295 -k file_access
-a always,exit -F arch=b64 -S creat -S open -S openat -S open_by_handle_at -S truncate -S ftruncate -F exit=-EPERM -F auid>=500 -F auid!=4294967295 -k file_access

# PCI-DSS v3.1 compliant rules

## 30. Access to all audit trails
-a always,exit -F dir=/var/log/audit/ -F perm=r -F auid>=1000 -F auid!=unset -F key=10.2.3-access-audit-trail
-a always,exit -F path=/usr/sbin/ausearch -F perm=x -F key=10.2.3-access-audit-trail
-a always,exit -F path=/usr/sbin/aureport -F perm=x -F key=10.2.3-access-audit-trail
-a always,exit -F path=/usr/sbin/aulast -F perm=x -F key=10.2.3-access-audit-trail
-a always,exit -F path=/usr/sbin/aulastlogin -F perm=x -F key=10.2.3-access-audit-trail
-a always,exit -F path=/usr/sbin/auvirt -F perm=x -F key=10.2.3-access-audit-trail

## 31. All elevation of privileges is logged
-a always,exit -F arch=b64 -S setuid -F a0=0 -F exe=/usr/bin/su -F key=10.2.5.b-elevated-privs-session
-a always,exit -F arch=b64 -S setresuid -F a0=0 -F exe=/usr/bin/sudo -F key=10.2.5.b-elevated-privs-session
-a always,exit -F arch=b64 -S execve -C uid!=euid -F euid=0 -F key=10.2.5.b-elevated-privs-setuid

## 32. Time data is protected.
-a always,exit -F arch=b64 -S adjtimex,settimeofday -F key=10.4.2b-time-change
-a always,exit -F arch=b64 -S clock_settime -F a0=0x0 -F key=10.4.2b-time-change
-w /etc/localtime -p wa -k 10.4.2b-time-change

## 33. Use file-integrity monitoring or change-detection software on logs
-a always,exit -F dir=/var/log/audit/ -F perm=wa -F key=10.5.5-modification-audit
```

After saving the rules there is a need to restart auditd service:

```shell
sudo systemctl restart auditd
```

Applied rules can be verified using:

```shell
sudo auditctl -l
```

### 5.2.12 Disable AIDE scan from crontab

AIDE is optional and currently not tested feature of Ubuntu so it has to be disabled:

```shell
sudo crontab -e
```

- Comment out line with AIDE check:

```sh
# Schedule CIS Checks via crontab
0 01 * * * su - root -c /sysmgt/bin/cis.checks.ubuntu.sh 1>/sysmgt/log/cis.checks.ubuntu.log 2>&1
#
# Run AIDE check - uncomment if needed
#0 5 * * * /usr/sbin/aide --check
```

### 5.2.13 Lock root account, remove its password and expiration

```shell
sudo passwd -dl root
sudo chage -M -1 root
```

### 5.2.14 Change GRUB password

GRUB in Atos golden image is password protected. "ubuntugrub" user is used for authentication. Password has to be changed by following steps:

- Enter root user

```shell
sudo -i
```

- Generate hash for GRUB password by answering prompt from executed command

```shell
grub-mkpasswd-pbkdf2
```

- Example hash will be shown on the screen in following format:

```sh
grub.pbkdf2.sha512.10000.<hash string>
```

- Remove existing password

```shell
sudo sed -i "7d" /etc/grub.d/40_custom
```

- Inject newly generated password

```shell
sudo bash -c "echo 'password_pbkdf2 ubuntugrub <hash>' >> /etc/grub.d/40_custom"
```

where hash is entire string given after entering password when grub-mkpasswd-pbkdf2 command is issued.

- Activate GRUB configuration

```shell
grub-mkconfig -o /boot/grub/grub.cfg
```

### 5.2.15 Create /etc/DHC-release file  

Placed on the system level and in the template notes (via vSphere Client). The values are going to be filled automatically during the automated template build.

```shell
DHC_LINUX_TEMPLATE=""  
DHC_LINUX_TEMPLATE_VERSION=""  
DHC_LINUX_TEMPLATE_SOURCE=""  
DHC_LINUX_TEMPLATE_BUILT_BY=""  
DHC_LINUX_TEMPLATE_BUILT_DATE=""  
DHC_LINUX_TEMPLATE_BUILT_TIMESTAMP=""
DHC_LINUX_TEMPLATE_UPDATE_DATE=""  
DHC_LINUX_TEMPLATE_UPDATE_TIMESTAMP=""
DHC_LINUX_DISTRIBUTION=""  
DHC_DOCUMENTATION=""  
DHC_RELEASE_NAME=""  
DHC_RELEASE_VERSION=""  
```

Example for current build:

```shell
DHC_LINUX_TEMPLATE="DHC.Next_Linux_U22"
DHC_LINUX_TEMPLATE_VERSION="1.4"
DHC_LINUX_TEMPLATE_SOURCE="Atos_Ubuntu_22.04.2_v1.0.iso"
DHC_LINUX_TEMPLATE_BUILT_BY="a558449"
DHC_LINUX_TEMPLATE_BUILT_DATE="11/09/2023"
DHC_LINUX_TEMPLATE_BUILT_TIMESTAMP="1694431734"
DHC_LINUX_TEMPLATE_UPDATE_DATE="04/06/2025"
DHC_LINUX_TEMPLATE_UPDATE_TIMESTAMP="1749031156"
DHC_LINUX_DISTRIBUTION="Ubuntu 22.04.2 LTS (Jammy Jellyfish)"
DHC_DOCUMENTATION="https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/workInstructions/dhcLinuxTemplateConfiguration.md"
DHC_RELEASE_NAME="DHC"
DHC_RELEASE_VERSION="2.0"
```

### 5.2.16 Disable unattended-upgrades service

Due to a random problem with installing any apt package on Ubuntu 22 called dpkg package frontend lock which causes an execution failure of stage 1 deployment playbooks of Linux machines. This is caused by unattended-upgrades service. It has to be disabled on the template level as patching in VCS is an attended process

```shell
sudo systemctl stop unattended-upgrades
sudo systemctl disable unattended-upgrades
sudo systemctl stop apt-daily.timer
sudo systemctl disable apt-daily.timer
sudo systemctl stop apt-daily.service
sudo systemctl disable apt-daily.service
sudo systemctl stop apt-daily-upgrade.timer
sudo systemctl disable apt-daily-upgrade.timer
sudo systemctl stop apt-daily-upgrade.service
sudo systemctl disable apt-daily-upgrade.service
```

### 5.2.17 Disable cloud-init

```shell
sudo touch /etc/cloud/cloud-init.disabled
```

### 5.2.18 Login as user "next" and perform a cleanup

```shell
sudo userdel -r atosadm
sudo rm /etc/netplan/*
sudo netplan generate

history -c
sudo bash -c "echo > /var/log/lastlog"
```

The prepared image can be now shut down and exported as a template
