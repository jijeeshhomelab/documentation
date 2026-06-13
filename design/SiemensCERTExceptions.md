# Siemens CERT Exceptions and False Positives

- Table of Contents
{:toc}

### Changelog

| Date | TOS | Issue | Author | Description |
| --- | --- | --- | --- | --- |
| 13.12.2021 | VCS 1.3 | DHC-3288 | Kathirvel Krishnasamy | Initial document creation |
| 01.02.2022 | VCS 1.5 | DHC-3934 | Kathirvel Krishnasamy | Siemens CERT Measure update |
| 28.02.2022 | VCS 1.5 | DHC-3956 | Kathirvel Krishnasamy | Update on section 2.1.2 |
| 28.03.2022 | VCS 1.5 | DHC-3961 | Kathirvel Krishnasamy | Update on section 2.1.1.1 |
| 11.04.2022 | VCS 1.5 | DHC-4456 | Kathirvel Krishnasamy | Update on CERT hyperlink and IIS CERT section 2.1.1.2 |
| 18.05.2022 | VCS 1.5 | DHC-4482 | Kathirvel Krishnasamy | Update on section 2.1.1.1 Windows CERT |
| 08.09.2022 | VCS 1.5 | DHC-4482 | Kathirvel Krishnasamy | Update on section 2.1.1.1 Windows CERT |
| 16.11.2022 | VCS 1.6 | CESDHC-4963 | Kathirvel Krishnasamy | Update on section 2.1.1.1 Windows CERT |
| 30.12.2022 | VCS 1.6 | CESDHC-5410 | Kathirvel Krishnasamy | Update on section 2.1.1 Windows and IIS CERT |
| 19.01.2023 | VCS 1.6 | CESDHC-5614 | Abhishek Sawant | Update on section 2.1.1 Windows CERT |
| 24.02.2026 | VCS 2.2 | VCS-15538 | Przemyslaw Pakula | Added Security Requirements Coverage, fixed lint issues |

# 1 Introduction

## 1.1 Purpose

The purpose of this document is to provide the information of Siemens CERT exception and false positives.

## 1.2 Audience

This document is intended for Atos Cloud Services Engineers and Architects responsible for VMware Cloud Services (VCS) solution implementation and maintenance.

## 1.3 Related Documents

### Security Requirements Coverage

| Instruction Name | Short Description |
| :---: | --- |
| [lldADSecurityEnhancement2024.md](lldADSecurityEnhancement2024.md) | Describes AD vulnerabilities in VCS and the remediation actions for key security findings. |
| [lldDhcRoleBasedAccessControl.md](lldDhcRoleBasedAccessControl.md) | Defines RBAC roles, mappings, and access review principles for VCS components. |
| [lldBreakTheGlass.md](lldBreakTheGlass.md) | Defines emergency access workflows for outage scenarios and recovery procedures. |
| [lldHardening.md](lldHardening.md) | Defines required hardening activities before production handover, including identity, firewall, and compliance controls. |
| [lldHashicorpVault.md](lldHashicorpVault.md) | Describes secure secret-management architecture, authentication methods, and audit logging. |
| [lldVulnerabilityManagement.md](lldVulnerabilityManagement.md) | Defines Nessus-based vulnerability scanning design, scope, and operating model. |
| [lldSecurityPosture.md](lldSecurityPosture.md) | Provides a consolidated overview of VCS security controls across encryption, scanning, RBAC, logging, and patching. |
| [SecurityMeasureExceptions.md](SecurityMeasureExceptions.md) | Lists approved Nessus/Alcatraz exceptions and false positives with rationale and mitigation context. |
| [SiemensCERTExceptions.md](SiemensCERTExceptions.md) | Lists Siemens CERT exceptions/false positives with applicability and risk/mitigation notes. |
| [lldSOXDB.md](lldSOXDB.md) | Describes SOXDB integration security controls, including credential handling, encryption, and RBAC. |
| [lldRemoteConsoleAccess.md](lldRemoteConsoleAccess.md) | Defines secure remote console access controls, including RBAC and certificate handling. |

## 2 List of Exceptions and False Positives

### 2.1 Siemens CERT

#### 2.1.1 Windows and IIS CERT

##### 2.1.1.1 Windows CERT

The detailed description for each Windows CERT Measure is available in [Windows CERT](files/lldSiemensCERTExceptions/bL968ReleaseOf20211011EnWindows2016.pdf)

| Measure No. | Exception | False Positives | Not Applicable | Reason | Source | Risk And Mitigation | Approval |
| --- | --- | --- | --- | --- | --- | --- | --- |
| BL968-0004 - Reset Password of kbrtgt Account Regularly (DC only) | - | - | TSS, ICA and WUS | This is applicable only to Domain Controllers | Windows CERT Document | - | TBD |
| BL968-0056 - Configure ‘Network security: Restrict NTLM: Audit NTLM authentication in this domain’ (DC only) | - | - | TSS, ICA and WUS | This is applicable only to Domain Controllers | Windows CERT Document | - | TBD |
| BL968-0156 - Configure ‘KDC support for claims, compound authentication and Kerberos armoring’ (DC only) | - | - | TSS, ICA and WUS | This is applicable only to Domain Controllers | Windows CERT Document | - | TBD |
| BL968-0361 - Ensure ‘Audit Kerberos Authentication Service’ is set to ‘Success and Failure’ (DC Only) | - | - | TSS, ICA and WUS | This is applicable only to Domain Controllers | Windows CERT Document | - | TBD |
| BL968-0486 - Configure ‘Audit User Mailbox Attribute’ (DC only) | - | - | TSS, ICA, WUS and ADC | Management stack does not have exchange/user mailbox | Windows CERT Document | - | TBD |
| BL968-0511 - Ensure ‘Audit Kerberos Service Ticket Operations’ is set to ‘Success  and Failure’ (DC Only) | - | - | TSS, ICA and WUS | This is applicable only to Domain Controllers | Windows CERT Document | - | TBD |
| BL968-0529 - Ensure ‘Turn On Virtualization Based Security: Credential Guard Configuration’ is set to ‘Enabled with UEFI lock’ (MS Only) | - | - | TSS, ICA, WUS and ADC | In VCS the VMs are installed with BIOS and not UEFI | Windows CERT Document | - | TBD |
| BL968-0612 - Ensure LAPS AdmPwd GPO Extension / CSE is installed (MS only) | TSS, ICA and WUS | - | ADC | As VCS already has different local user password created by automation and stored in Hashi | Windows CERT Document | TBD | TBD |
| BL968-1081 - Ensure ‘Accounts: Guest account status’ is set to ‘Disabled’ (MS only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-1186 - Ensure ‘Hardened UNC Paths’ is set to ‘Enabled, with “Require Mutual Authentication” and “Require Integrity” set for all NETLOGON and SYSVOL(DC only) shares’ | - | ADC | TSS, ICA and WUS | Applicable to DC only still flagged for MS | Windows CERT Document | - | TBD |
| BL968-1191 - Ensure ‘Apply UAC restrictions to local accounts on network logons’ is set to ‘Enabled’ (MS only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-1206 - Ensure ‘Domain controller: LDAP server signing requirements’ is set to ‘Require signing’ (DC only) | - | - | TSS, ICA and WUS | This is applicable only to Domain Controllers | Windows CERT Document | - | TBD |
| BL968-1516 - Ensure ‘Restrict Unauthenticated RPC clients’ is set to ‘Enabled: Authenticated’ (MS only) | ADC | - | - | As per CERT it applies to 'MS Only' but the settings are valid to apply it on Domain Controller too and will impact functionality. Hence, this policy setting should never be applied to a Domain Controller | Windows CERT Document | TBD | TBD |
| BL968-1571 - Ensure ‘Interactive logon: Number of previous logons to cache (in case domain controller is not available)’ is set to ‘0’ (MS only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-1611 - Ensure ‘Audit Directory Service Changes’ is set to ‘Success’ (DC only) | - | - | TSS, ICA and WUS | This is applicable only to Domain Controllers | Windows CERT Document | - | TBD |
| BL968-1666 - Ensure ‘Enumerate local users on domain-joined computers’ is set to ‘Disabled’ (MS only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-1736 - Ensure ‘Audit Computer Account Management’ is set to ‘Success’ (DC only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-2311 - Ensure ‘Interactive logon: Require Domain Controller Authentication to unlock workstation’ is set to ‘Enabled’ (MS only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-2351 - Ensure ‘Network access: Restrict clients allowed to make remote calls to SAM’ is set to ‘Administrators: Remote Access: Allow’ (MS only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-2816 - Ensure ‘Prohibit connection to non-domain networks when connected to domain authenticated network’ is set to ‘Enabled’ (MS only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-2851 - Ensure ‘Enable Local Admin Password Management’ is set to ‘Enabled’ (MS only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-2956 - Ensure ‘Audit Other Account Management Events’ is set to ‘Success’ (DC only) | - | - | TSS, ICA and WUS | This is applicable only to Domain Controllers | Windows CERT Document | - | TBD |
| BL968-3176 - Ensure ‘Password Settings: Password Complexity’ is set to ‘Enabled: Large letters + small letters + numbers + special characters’ (MS only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-3291 - Ensure ‘Enable RPC Endpoint Mapper Client Authentication’ is set to ‘Enabled’ (MS only) | - | - | ADC | This is applicable only to Member Servers | Windows CERT Document | - | TBD |
| BL968-3986 - Ensure ‘Add workstations to domain’ is set to ‘Administrators’ (DC only) | - | ADC | TSS, ICA and WUS | This is applicable to Domain controllers only. Checked and found only Administrators can do this and this is expected value | Windows CERT Document | - | TBD |
| BL968-4586 - Ensure ‘Audit Directory Service Access’ is set to ‘Failure’ (DC only) | - | - | TSS, ICA and WUS | This is applicable only to Domain Controllers | Windows CERT Document | - | TBD |
| BL968-4626 - Ensure ‘Do not allow password expiration time longer than required by policy’ is set to ‘Enabled’ (MS only) | TSS, ICA and WUS | - | ADC | We have configured policy that will enforce password change and we have well defined policy which manages password policy. | Windows CERT Document | - | TBD |
| BL968-4741 - Ensure ‘Audit Distribution Group Management’ is set to ‘Success’ (DC only) | - | - | TSS, ICA and WUS | This is applicable only to Domain Controllers | Windows CERT Document | - | TBD |
| BL968-4845 - Ensure ‘Extended Protection for LDAP Authentication (Domain Controllers only)’ is set to ‘Enabled: Enabled, always (recommended)’ (DC Only) | ADC | - | TSS, ICA and WUS | KB4025339 is unavailable/removed from Microsoft Download site and this is one of the pre-requites to avoid compatibility issues. Hence, this will be considered as an exception. | Windows CERT Document | TBD | TBD |
| BL968-4859 - Ensure ‘Enable Windows NTP Server’ is set to ‘Disabled’ (MS only) | ADC | - | TSS, ICA and WUS | As per CERT it is applies to MS only but this is applicable to Domain Controller too. In most enterprise managed environments, you should not disable the Windows NTP Server on Domain Controllers. | Windows CERT Document | TBD | TBD |
| BL968-5341 - Ensure ‘Password Settings: Password Age (Days)’ is set to ‘Enabled: 45 or fewer’ (MS only) | TSS, ICA, WUS and ADC | - | - | This rule is not mandatory, if another solution (software solution or approved organizational process) is used to follow the Siemens rules for local Administrator passwords. | Windows CERT Document | - | TBD |
| BL968-5551 - Ensure ‘Password Settings: Password Length’ is set to ‘Enabled: 15 or more’ (MS only) | TSS, ICA, WUS and ADC | - | - | This rule is not mandatory, if another solution (software solution or approved organizational process) is used to follow the Siemens rules for local Administrator passwords. | Windows CERT Document | - | TBD |
| BL968-5631 - Ensure ‘Network access: Do not allow anonymous enumeration of SAM accounts and shares’ is set to ‘Enabled’ (MS only) | - | - | ADC | This policy has no effect on Domain Controllers. | Windows CERT Document | - | TBD |
| BL968-5726 - Ensure ‘Network access: Do not allow anonymous enumeration of SAM accounts’ is set to ‘Enabled’ (MS only) | - | - | ADC | This policy has no effect on Domain Controllers. | Windows CERT Document | - | TBD |
| BL968-0003 - Configure ‘Intel AMT Login Bypass’ | - | - | TSS, ICA, WUS and ADC | This rule is not mandatory for systems with no BIOS/UEFI configuration capabilities such as cloud instances in AWS / Azure, offering this configuration possibility. | Windows CERT Document | - | TBD |
| BL968-0007 - Ensure ‘Application: Specify the maximum log file size (KB)’ is set to ‘Enabled: 131,072 or greater’ | TSS, ICA, WUS and ADC | - | - | Hoping that all the applications are sending the logs to vRLI | Windows CERT Document | - | TBD |
| BL968-0017 - Limit Additional Server Roles | - | - | TSS, ICA, WUS and ADC | Covered by Design | Windows CERT Document | - | TBD |
| BL968-0031 - Require TPM 2.0 | - | - | TSS, ICA, WUS and ADC | Hardware Security | Windows CERT Document | - | TBD |
| BL968-0032 - Configure ‘Windows Server Features’ | - | - | TSS, ICA, WUS and ADC | Covered by Design | Windows CERT Document | - | TBD |
| BL968-0049 - Domain Controller Hardening | ADC | - | TSS, ICA and WUS | "Before building up a Domain Controller in Siemens’ Active Directory, you need to get in contact with the OneAD team to clarify the current hardening requirements for a Domain Controller" | Windows CERT Document | - | TBD |
| BL968-0066 - Enable an Endpoint Detection and Response Agent | - | - | TSS, ICA, WUS and ADC | TBD | Windows CERT Document | - | TBD |
| BL968-0100 - Ensure ‘Encryption Oracle Remediation’ is set to ‘Enabled: Force Updated Clients’ | TSS, ICA, WUS and ADC | - | - | Breaks Remote desktop functionality | Windows CERT Document | - | TBD |
| BL968-0110 - Configure ‘Active Directory Domain Membership’ | - | - | TSS, ICA, WUS and ADC | The rule is not mandatory, if the system is connected to a separated, isolated network and no direct connection from the Siemens Intranet and from other Siemens systems is possible. | Windows CERT Document | - | TBD |
| BL968-0117 - Configure ‘SMBv3 protocol encryption’ | TSS, ICA, WUS and ADC | - | - | This rule is not mandatory for systems which ensure, that all SMB connections are encrypted. i.e., rule BL968-0117 Configure ‘SMBv3 protocol encryption’ as well as rule BL968-0118 Do Not Allow Unencrypted SMB Sessions are implemented on those systems. | Windows CERT Document | - | TBD |
| BL968-0172 - Install Windows using UEFI mode | - | - | TSS, ICA, WUS and ADC | In VCS the VMs are installed with BIOS and not UEFI | Windows CERT Document | - | TBD |
| BL968-0249 - Update BIOS/UEFI Firmware | - | - | TSS, ICA, WUS and ADC | In VCS the VMs are installed with BIOS and not UEFI | Windows CERT Document | - | TBD |
| BL968-0271 - Special Protection of High-Value Accounts | - | - | TSS, ICA, WUS and ADC | Role based access in VCS | Windows CERT Document | - | TBD |
| BL968-0291 - Configure ‘Enforce drive encryption type on operating system drives’ | TSS, ICA, WUS and ADC | - | - | This rule is not mandatory, if all local drives are encrypted with an AES256 equivalent cipher (or stronger) by other means (e.g., storage solution with encryption capabilities) | Windows CERT Document | - | TBD |
| BL968-0326 - Configure ‘Choose drive encryption method and cipher strength (Windows 10 [Version 1511] and later)’ | - | - | TSS, ICA, WUS and ADC | Not Applicable for Windows 2016 | Windows CERT Document | - | TBD |
| BL968-0331 - Configure ‘Configure minimum PIN length for startup’ | TSS, ICA, WUS and ADC | - | - | This Rule is not mandatory, Since we have Storage Solution with Encryption capabilites | Windows CERT Document | - | TBD |
| BL968-0336 - Configure ‘Enforce drive encryption type on fixed data drives’ | TSS, ICA, WUS and ADC | - | - | This rule is not mandatory, if all local drives are encrypted with an AES256 equivalent cipher (or stronger) by other means (e.g., storage solution with encryption capabilities) | Windows CERT Document | - | TBD |
| BL968-0362 - Enable UEFI Secure Boot | - | - | TSS, ICA, WUS and ADC | Hardware Security | Windows CERT Document | - | TBD |
| BL968-0442 - Configure ‘Secure Built-in Remote Management Interface’ | - | - | TSS, ICA, WUS and ADC | Hardware Security | Windows CERT Document | - | TBD |
| BL968-0445 - Ensure ‘Turn On Virtualization Based Security’ is set to ‘Enabled’ | TSS, ICA, WUS and ADC | - | - | Virtualization Based Security requires a 64-bit version of Windows with Secure Boot enabled, which in turn requires that Windows was installed with a UEFI BIOS configuration, not a Legacy BIOS configuration. In VCS the VMs are installed with BIOS and not UEFI. | Windows CERT Document | - | TBD |
| BL968-0452 - Disable Compatibility Support Module | - | - | TSS, ICA, WUS and ADC | This rule is not mandatory for systems with no BIOS/UEFI configuration capabilities such as cloud instances | Windows CERT Document | - | TBD |
| BL968-0491 - Configure ‘Allow network unlock at startup’ | TSS, ICA, WUS and ADC | - | - | This Rule is not mandatory, Since we have Storage Solution with Encryption capabilites. | Windows CERT Document | - | TBD |
| BL968-0620 - Set a Password to Access BIOS/UEFI Setup | - | - | TSS, ICA, WUS and ADC | Hardware Security | Windows CERT Document | - | TBD |
| BL968-0686 - Configure ‘Network security: Restrict NTLM: Incoming NTLM traffic’ | TSS, ICA, WUS and ADC | - | - | This rule is not mandatory, if both of the following requirements are satisfied: All WinRM traffic is encrypted, e.g., by using HTTPs according BL968-3126. Incoming connections are IP-based filtered to dedicated hosts mandatorily requiring WinRM access, such as Ansible-Tower systems. | Windows CERT Document | - | TBD |
| BL968-0769 - Subscribe to the Cybersecurity Governance Announcement Service | - | - | TSS, ICA, WUS and ADC | General rules from Siemens intrasite and select the topic Manage Subscription. The upcoming webpage intranet.siemens.com will let you know if you are subscribed to this service or not. | Windows CERT Document | - | TBD |
| BL968-0841 - Configure ‘Require additional authentication at startup’ | TSS, ICA, WUS and ADC | - | - | This Rule is not mandatory, Since we have Storage Solution with Encryption capabilites. | Windows CERT Document | - | TBD |
| BL968-0846 - Ensure ‘Turn On Virtualization Based Security: Select Platform Security Level’ is set to ‘Secure Boot’ | TSS, ICA, WUS and ADC | - | - | Virtualization Based Security requires a 64-bit version of Windows with Secure Boot enabled, which in turn requires that Windows was installed with a UEFI BIOS configuration, not a Legacy BIOS configuration. In VCS the VMs are installed with BIOS and not UEFI. | Windows CERT Document | - | TBD |
| BL968-0986 - Set a BIOS/UEFI Boot Password | - | - | TSS, ICA, WUS and ADC | Hardware Security | Windows CERT Document | - | TBD |
| BL968-0988 - Enable Virtualization Technology | - | - | TSS, ICA, WUS and ADC | Hardware Security | Windows CERT Document | - | TBD |
| BL968-1066 - Use Domain User Accounts | - | - | TSS, ICA, WUS and ADC | No local accounts in Domain controller | Windows CERT Document | - | TBD |
| BL968-1101 - Configure the policy ‘Configure use of passwords for removable data drives’ | TSS, ICA, WUS and ADC | - | - | In VCS Bitlocker is not enabled and using Storage encryption hence any bitlocker setting is not configured | Windows CERT Document | - | TBD |
| BL968-1431 - Ensure ‘Windows Firewall: Public: Settings: Apply local firewall rules’ is set to ‘No’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-1444 - Ensure ‘Turn On Virtualization Based Security: Virtualization Based Protection of Code Integrity’ is set to ‘Enabled with UEFI lock’ | TSS, ICA, WUS and ADC | - | - | VCS uses BIOS this need UEFI | Windows CERT Document | - | TBD |
| BL968-1462 - Ensure ‘Allow network connectivity during connected-standby (plugged in)’ is set to ‘Disabled’ | - | - | TSS, ICA, WUS and ADC | Network connectivity in standby (while plugged in) is not guaranteed. This connectivity restriction currently only applies to WLAN networks only | Windows CERT Document | - | TBD |
| BL968-1636 - Ensure ‘Choose how BitLocker-protected fixed drives can be recovered’ is set to ‘Disabled’ | TSS, ICA, WUS and ADC | - | - | In VCS Bitlocker is not enabled and using Storage encryption hence any bitlocker setting is not configured | Windows CERT Document | - | TBD |
| BL968-1641 - Configure ‘Turn off Automatic Download and Install of updates’ | TSS, ICA, WUS and ADC | - | - | This will override schedule patch window | Windows CERT Document | - | TBD |
| BL968-1646 - Ensure ‘Windows Firewall: Private: Logging: Log dropped packets’ is set to ‘Yes’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-1671 - Configure the policy ‘Control use of BitLocker on removable drives’ | TSS, ICA, WUS and ADC | - | - | In VCS Bitlocker is not enabled and using Storage encryption hence any bitlocker setting is not configured | Windows CERT Document | - | TBD |
| BL968-1741 - Ensure ‘Windows Firewall: Domain: Inbound connections’ is set to ‘Block (default)’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-1871 - Ensure ‘Choose how BitLocker-protected operating system drives can be recovered’ is set to ‘Disabled’ | TSS, ICA, WUS and ADC | - | - | In VCS Bitlocker is not enabled and using Storage encryption hence any bitlocker setting is not configured | Windows CERT Document | - | TBD |
| BL968-1901 - Ensure ‘Windows Firewall: Private: Logging: Name’ is configured | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-2211 - Ensure ‘Audit Security State Change’ is set to ‘Success’ | TSS, ICA, WUS and ADC | - | - | VCS has Success and Failure, expected was Success only | Windows CERT Document | - | TBD |
| BL968-2381 - Ensure ‘Audit Security System Extension’ is set to ‘Success’ | TSS, ICA, WUS and ADC | - | - | VCS has Success and Failure, expected was Success only | Windows CERT Document | - | TBD |
| BL968-2596 - Ensure ‘WinRM Service: Allow remote server management through WinRM’ is set to ‘Disabled’ | TSS, ICA, WUS and ADC | - | - | This rule is not mandatory, if both of the following requirements are satisfied: All WinRM traffic is encrypted, e.g., by using HTTPs according BL968-3126. Incoming connections are IP-based filtered to dedicated hosts mandatorily requiring WinRM access, such as Ansible-Tower systems. | Windows CERT Document | - | TBD |
| BL968-2696 - Ensure ‘Windows Firewall: Public: Inbound connections’ is set to ‘Block (default)’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-2706 - Ensure ‘Windows Firewall: Public: Logging: Log dropped packets’ is set to ‘Yes’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-3226 - Ensure ‘Windows Firewall: Domain: Firewall state’ is set to ‘On (recommended)’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-3446 - Ensure ‘Windows Firewall: Domain: Logging: Name’ is configured | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-3486 - Ensure ‘Windows Firewall: Private: Inbound connections’ is set to ‘Block (default)’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-3514 - Ensure ‘Allow Remote Shell Access’ is set to ‘Disabled’ Administrative Templates (User) | TSS, ICA, WUS and ADC | - | - | This rule is not mandatory, if both of the following requirements are satisfied: All WinRM traffic is encrypted, e.g., by using HTTPs according BL968-3126. Incoming connections are IP-based filtered to dedicated hosts mandatorily requiring WinRM access, such as Ansible-Tower systems. | Windows CERT Document | - | TBD |
| BL968-4526 - Ensure ‘Windows Firewall: Public: Logging: Size limit (KB)’ is configured | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-4701 - Ensure ‘System: Specify the maximum log file size (KB)’ is set to ‘Enabled: 131,072 or greater’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-4812 - Enable IOMMU Technology | - | - | TSS, ICA, WUS and ADC | Hardware Security | Windows CERT Document | - | TBD |
| BL968-4849 - Disable IPv6 (Ensure TCPIP6 Parameter ‘DisabledComponents’ is set to ‘0xff (255)’) | - | - | TSS, ICA, WUS and ADC | This rule is not mandatory, if IPv6 is completely disabled due to BL968-4849 Disable IPv6 | Windows CERT Document | - | TBD |
| BL968-4871 - Ensure ‘Turn on behavior monitoring’ is set to ‘Enabled’ | TSS, ICA, WUS and ADC | - | - | Dedicated AV is in place, hence Defender does not need to scan files. | Windows CERT Document | - | TBD |
| BL968-4911 - Ensure ‘Windows Firewall: Public: Logging: Log successful connections’ is set to ‘Yes’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-5031 - Configure ‘Log on as a service’ | TSS, ICA, WUS and ADC | - | - | TBD | Windows CERT Document | - | TBD |
| BL968-5061 - Ensure ‘Windows Firewall: Public: Logging: Name’ is configured | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-5086 - Ensure ‘Windows Firewall: Public: Firewall state’ is set to ‘On (recommended)’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-5121 - Ensure ‘Windows Firewall: Private: Logging: Size limit (KB)’ is configured | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-5141 - Ensure ‘Windows Firewall: Private: Logging: Log successful connections’ is set to ‘Yes’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-5291 - Ensure ‘Windows Firewall: Domain: Logging: Log successful connections’ is set to ‘Yes’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-5391 - Ensure ‘Audit Security Group Management’ is set to ‘Success’ | TSS, ICA, WUS and ADC | - | - | VCS has Success and Failure, expected was Success only | Windows CERT Document | - | TBD |
| BL968-5446 - Ensure ‘Windows Firewall: Public: Settings: Apply local connection security rules’ is set to ‘No’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-5486 - Ensure ‘Windows Firewall: Domain: Logging: Size limit (KB)’ is configured | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-5576 - Ensure ‘Windows Firewall: Private: Firewall state’ is set to ‘On (recommended)’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-5656 - Ensure ‘Windows Firewall: Public: Outbound connections’ is set to ‘Block’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-5741 - Ensure ‘NetBT NodeType configuration’ is set to ‘Enabled: P-node (recommended)’ | - | - | TSS, ICA, WUS and ADC | Not Applicable for Windows 2016 | Windows CERT Document | - | TBD |
| BL968-5881 - Ensure ‘Enable computer and user accounts to be trusted for delegation’ is configured | - | ADC | - | This is approved account as per the CERT document for Administrators in Domain Controllers | Windows CERT Document | - | TBD |
| BL968-5971 - Ensure ‘Windows Firewall: Domain: Logging: Log dropped packets’ is set to ‘Yes’ | TSS, ICA, WUS and ADC | - | - | Windows Firewall is disabled on the servers | Windows CERT Document | - | TBD |
| BL968-7113 - Ensure ‘Setup: Specify the maximum log file size (KB)’ is set to ‘Enabled: 131,072 or greater’ | TSS, ICA, WUS and ADC | - | - | All the applications are sending the logs to vRLI | Windows CERT Document | - | TBD |
| BL968-7367 - Ensure ‘Prevent memory overwrite on restart’ is set to ‘Disabled’ | TSS, ICA, WUS and ADC | - | - | Bitlocker is not enabled in VCS | Windows CERT Document | - | TBD |
| BL968-9206 - Ensure ‘Choose how BitLocker-protected removable drives can be recovered’ is set to ‘Disabled’ | TSS, ICA, WUS and ADC | - | - | In VCS Bitlocker is not enabled and using Storage encryption hence any bitlocker setting is not configured | Windows CERT Document | - | TBD |
| BL968-0586 - Ensure ‘Require user authentication for remote connections by using Network Level Authentication’ is set to ‘Enabled’ | TSS, ICA, WUS and ADC | - | - | Remote desktop is not working | Windows CERT Document | - | TBD |
| BL968-0117 - Configure ‘SMBv3 protocol encryption’ | TSS, ICA, WUS and ADC | - | - | If we enable this setting then reports exported from vROPs can not be delivered to windows server | Windows CERT Document | - | TBD |
| BL968-1206 - Ensure ‘Domain controller: LDAP server signing requirements’ is set to ‘Require signing’ (DC only) | ADC | - | - | Hashicorp ldap connectivity breaks | Windows CERT Document | - | TBD |
| BL968-5016 - Ensure ‘Network access: Do not allow storage of passwords and credentials for network authentication’ is set to ‘Enabled’ | TSS, ICA, WUS and ADC | - | - | Capacity report is not working after applying this Measure | Windows CERT Document | - | TBD-CESDHC-3859 |
| BL968-0325 - Configure TLS Cipher Suite Ordering | TSS, ICA, WUS and ADC | - | - | Remote Desktop is not working | Windows CERT Document | - | TBD |
| BL968-3116 - Ensure ‘Accounts: Administrator account status’ is set to ‘Disabled’ (MS only) | ADC | - | - | This is applicable only to Members Servers only | Windows CERT Document | - | TBD |
| BL968-3256_Configure-Turn-off-background-refresh-of-Group-Policy | - | TSS, ICA, WUS and ADC | - | Remediation in GPO has been applied but not reflected in the Windows servers | Windows CERT Document | - | TBD |
| BL968-0981 - Configure ‘Network security: Restrict NTLM: NTLM authentication in this domain’ | TSS, ICA, WUS and ADC | - | - | Remote desktop is not working | Windows CERT Document | - | TBD |
| BL968-0009_Configure-Retention-method-for-application-log | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0052_Configure-TLS-1.2-Protocol-in-WinHTTP | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0076_Configure-DES-and-3DES-Cipher-in-Schannel | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0082_Configure-Retention-method-for-security-log | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0116_Configure-SMBv1-Protocol | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0126_Configure-Retain-system-log | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0166_Configure-Audit-Process-Termination | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0220_Configure-Retention-method-for-system-log | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0306_Configure-Shutdown-Clear-virtual-memory-pagefile | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0311_Configure-Devices-Allow-undock-without-having-to-log-on | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0316_Configure-Retain-application-log | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0396_Configure-Maximum-security-log-size | - | TSS, ICA, WUS and ADC | - | This measure has been removed from CERT document but still the scanner is detecting as non-compliantThis measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0466_Configure-Maximum-system-log-size | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0496_Configure-Devices-Restrict-CD-ROM-access-to-locally-logged-on-user-only | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0631_Configure-System-cryptography-Use-Certificate-Rules-on-Windows-Executables-for-Software-Restriction | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0681_Configure-Retain-security-log | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0716_Configure-Maximum-application-log-size | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL942-1251_Ensure-Deny-access-to-this-computer-from-the-network-is-configured | - | TSS, ICA, WUS and ADC | - | This measure applies to Windows Server 2019 and the scanner is detected as non-compliant | Windows CERT Document | - | TBD |
| BL968-0566_Configure-Allow-delegating-default-credentials-with-NTLM-only-server-authentication | TSS, ICA, WUS and ADC | - | - | This measure breaks Remote Desktop Connection. CESDHC-469 | Windows CERT Document | - | TBD |
| BL968-0567_Configure-Restrict-delegation-of-credentials-to-remote-servers | TSS, ICA, WUS and ADC | - | - | This measure breaks Remote Desktop Connection. CESDHC-469 | Windows CERT Document | - | TBD |
| BL968-1271_Configure-Block-launching-Windows-Store-apps-with-Windows-Runtime-API-access-from-hosted-content | - | TSS, ICA, WUS and ADC | - | This measure has been removed from CERT document but still the scanner is detecting as non-compliant | Windows CERT Document | - | TBD |
| BL968-1301_Configure-Prevent-using-Localhost-IP-address-for-WebRTC | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1696_Configure-Untrusted-Font-Blocking | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1721_Configure-Disable-pre-release-features-or-settings | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1751_Configure-Configure-cookies | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | - | TBD | |
| BL968-2611_Configure-Allow-InPrivate-Browsing | - | ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-2921_Configure-Configure-Password-Manager | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-3147_Configure-Audit-User-Device-Claims | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-3341_Configure-Configure-Pop-up-Blocker | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-4006_Configure-Accounts-Rename-guest-account | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-4861_Configure-Configure-search-suggestions-in-Address-bar | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-5816_Configure-Accounts-Rename-administrator-account | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-5911_Configure-Allow-Extensions | - | TSS, ICA, WUS and ADC | - | This measure has been removed from the CERT document but, the scanner is still detected as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0118_Do-Not-Allow-Unencrypted-SMB-Sessions | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0131_Configure-RC4-Cipher-in-Schannel | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0211_Configure-Kerberos-client-support-for-claims-compound-authentication-and-Kerberos-armoring | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0261_Configure-Default-Share-Permissions | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0484_Configure-Modulus-Size-in-the-DHE-Key-Exchange | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0531_Configure-Sharing-Wizard | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0581_Configure-Allow-delegating-default-credentials | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0641_Configure-Additional-LSA-Protection | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0821_Configure-RC2-Cipher-in-Schannel | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0896_Configure-Turn-off-Automatic-Root-Certificates-Update | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-0936_Configure-PowerShell-Turn-on-Script-Execution | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1000_Configure-Maximum-log-size-KB-of-TerminalServices-LocalSessionManager | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1001_Configure-Maximum-log-size-KB-of-PowerShell | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1046_Configure-Turn-off-Internet-Connection-Wizard-if-URL-connection-is-referring-to-Microsoft.com | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1051_Configure-Join-Microsoft-MAPS | - | ICA and WUS | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-2497_Configure-MSS-TcpMaxDataRetransmissions-How-many-times-unacknowledged-data-is-retransmitted | TSS, ICA, WUS and ADC | - | - | TCP starts a retransmission timer when each outbound segment is passed to the IP. If no acknowledgment is received for the data in a given segment before the timer expires, then the segment is retransmitted up to three times. | Windows CERT Document | - | TBD |
| BL968-2498_Configure-MSS-TcpMaxDataRetransmissionsIPv6-How-many-times-unacknowledged-data-is-retransmitted | TSS, ICA, WUS and ADC | - | - | TCP starts a retransmission timer when each outbound segment is passed to the IP. If no acknowledgment is received for the data in a given segment before the timer expires, then the segment is retransmitted up to three times. | Windows CERT Document | - | TBD |
| BL968-1071_Configure-Turn-on-Mapper-IO-LLTDIO-driver | - | TSS, ICA, WUS and ADC | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1181_Configure-Access-this-computer-from-the-network | - | TSS, ICA, WUS and ADC | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1276_Configure-Turn-off-handwriting-recognition-error-reporting | - | TSS, ICA, WUS and ADC | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1386_Configure-Minimize-the-number-of-simultaneous-connections-to-the-Internet-or-a-Windows-Domain | - | TSS, ICA, WUS and ADC | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-1461_Configure-Allow-network-connectivity-during-connected-standby-on-battery | - | TSS, ICA, WUS and ADC | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-2071_Configure-Turn-on-PowerShell-Transcription | - | TSS, ICA, WUS and ADC | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-2216_Configure-Turn-off-access-to-the-Store | - | TSS, ICA, WUS and ADC | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-2246_Configure-Turn-on-Responder-RSPNDR-driver | - | TSS, ICA, WUS and ADC | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-2286_Configure-Disable-all-apps-from-Windows-Store | - | TSS, ICA, WUS and ADC | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |
| BL968-2326_Configure-Turn-off-handwriting-personalization-data-sharing | - | TSS, ICA, WUS and ADC | - | Applied settings as recommended but the scanner still detects it as non-compliant. | Windows CERT Document | - | TBD |

##### 2.1.1.2 IIS CERT

The detailed description for each IIS CERT Measure is available in [Windows IIS CERT](files/lldSiemensCERTExceptions/bL115ReleaseOf20201030EnIIS.pdf)

| Measure No. | Exception | False Positive | Not Applicable | Reason | Source | Risk And Mitigation | Approval |
| --- | --- | --- | --- | --- | --- | --- | --- |
| BL115-0172_Subscribe to the Cybersecurity Governance Announcement Service | - | - | ICA and WUS | This is subscription for the servers. Siemens Cybersecurity Governance offers an Intranet page which lists all valid Cybersecurity Governance Announcements | IIS CERT Document | - | TBD |
| BL115-0304_Disable File and Printer Sharing | ICA and WUS | - | - | Windows default shares like SYSVOL, Admin share, GPOs, logon, logoff script sync from shared access stops when this measure is applied. | IIS CERT Document | - | TBD |
| BL115-0846_Set NTFS File System Permissions on Web Content Folders | - | ICA and WUS | - | Applied settings as per CERT but still the scan reports showing them as non-compliant | IIS CERT Document | - | TBD |
| BL115-1957_Ensure Default FTP log location is moved | - | - | ICA and WUS | FTP is not used in VCS Management Servers | IIS CERT Document | - | TBD |
| BL115-1956_Ensure ‘FTP Logging’ is enabled | - | - | ICA and WUS | FTP is not used in VCS Management Servers | IIS CERT Document | - | TBD |
| BL115-0356_Configure FTP Securely | - | - | ICA and WUS | FTP is not used in VCS Management Servers | IIS CERT Document | - | TBD |
| BL115-4651_Ensure FTP Requests are Encrypted | - | - | ICA and WUS | FTP is not used in VCS Management Servers | IIS CERT Document | - | TBD |
| BL115-1256_Configure FTP Logon Attempt Restrictions | - | - | ICA and WUS | FTP is not used in VCS Management Servers | IIS CERT Document | - | TBD |
| BL115-2563_Disable ‘MD5’ Hashing Algorithm | ICA and WUS | - | - | Breaks Remote desktop Connection | IIS CERT Document | - | TBD |
| BL115-2544_Disable ‘SHA-1’ Hashing Algorithm | ICA and WUS | - | - | Breaks Remote desktop Connection | IIS CERT Document | - | TBD |
| BL115-1891_Restrict Access to Sensitive Site Features | - | ICA and WUS | - | Shows as non-compliant after the remediation due to limited features configured in the server | IIS CERT Document | - | TBD |
| BL115-1426_Ensure ‘Forms Authentication’ is set to Require SSL | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-3491_Ensure ‘Forms Authentication’ is set to use Cookies | WUS | - | - | Breaks WUS patching and launching WUS console | IIS CERT Document | - | TBD |
| BL115-2711_Ensure Transport Layer Security for ‘Basic Authentication’ is Configured | WUS | - | - | It breaks WSUS server and patching gets failed | IIS CERT Document | - | TBD |
| BL115-2731_Ensure Global .NET Trust Level is Configured | WUS | - | - | It breaks WSUS server and patching gets failed | IIS CERT Document | - | TBD |
| BL115-5851_Ensure Unlisted File Extensions are not allowed | WUS | - | - | It breaks WSUS server and patching gets failed | IIS CERT Document | - | TBD |
| BL115-0192_Ensure ‘NTFS Auditing’ is Enabled | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-4906_Configure TLS Cipher Suite Ordering | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-8551_Configure Frame-ancestors | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-0725 - Configure a Unique Binding | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-2921 - Ensure Host Headers are Used | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-3336 - Ensure ‘Cookies’ are set with HttpOnly Attribute | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-9971 - Ensure ‘Cookies’ are set with RequireSSL to True | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-4911 - Ensure X-Powered-By Header is removed | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-8201 - Ensure Server Header is removed | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-2596 - Remove the Unwanted X-AspNet-Version Header | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-4416 - Ensure Non-ASCII Characters in URLs are not Allowed | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-1951 - Ensure ‘HTTP Trace Method’ is Disabled | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-1952 - Ensure ‘HTTP Options Method’ is Disabled | - | ICA and WUS | - | Shows as non-compliant after the remediation | IIS CERT Document | - | TBD |
| BL115-0514_Configure-Content-Security-Policy | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-0928_X-Frame-Options | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-1156_Enable-TLS-1.2-Protocol | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-1391_Ensure-Double-Encoded-Requests-will-be-Rejected | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-2386_Ensure-Sufficient-Size-for-DHE-Key-Shares | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-3243_Block-SQL-Injections | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-3321_Ensure-Unique-Application-Pools-is-set | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-4471_Configure-HSTS-Header | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-4481_Disable-AES-128-128-Cipher-Suites | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-4876_Ensure-MachineKey-Validation-Method-NET-4-5-is-Configured-for-SHA-2-Methods | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-5046_Ensure-Deployment-Method-Retail-is-set | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-5251_Ensure-Global-Authorization-Rule-is-set | - | ICA and WUS | - | Limited IIS modules installed | IIS CERT Document | - | TBD |
| BL115-5716_Ensure-Web-Content-is-on-a-Non-System-Partition | - | ICA and WUS | - | IIS Service is not working after the remediation | IIS CERT Document | - | TBD |

#### 2.1.2 Linux and Web Apache CERT

The detailed description for each Linux CERT Measure is available in [Linux CERT](files/lldSiemensCERTExceptions)

| Measure No. | Exception | False Positives | Not Applicable | Reason | Source | Risk And Mitigation | Approval |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M729001_Protecting physical interfaces | - | - | All Linux Servers | This measure is not applicable for cloud environments | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M680620_BIOS Settings - Boot and BIOS Passwords | - | - | All Linux Servers | This measure is not applicable for cloud environments | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M201280_Boot Sequence, Multi Boot | - | - | All Linux Servers | This measure is not applicable for cloud environments | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M338090_Prevent unauthenticated Boot | - | - | All Linux Servers | This measure is not applicable for cloud environments | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M700351_Secure Built-in Remote Management Interface | - | - | All Linux Servers | This measure is not applicable for cloud environments | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M112800_Protecting Linux Bootmanager with password | - | - | All Linux Servers | This measure is not applicable for cloud environments | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M950630_No Biometric Authentication Devices | - | - | All Linux Servers | This measure is not applicable for cloud environments | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M800954_Disable Firewire (IEEE1394) | - | - | All Linux Servers | This measure is not applicable for cloud environments | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M603120_Operating system version | All Linux Servers | - | - | Script is looking for RHEL Operating System | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M653700_Siemens CERT Security Telegrams | - | - | All Linux Servers | Subscribe to the Security Telegrams, test and implement Security Telegrams according to this measure | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M608700_Scheduled service security | - | All Linux Servers | - | The command exists on different path and as per CERT document /etc/cron.allow" AND /etc/at.allow" OR "/etc/cron.deny" AND /etc/at.deny", one set must exist. The  at.deny file already exist | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M802906_Protecting System Files | - | All Linux Servers | - | "The software mentioned in this measure are to be removed. As per VCS template documentation, dhcLinuxTemplateConfiguration.md – section 5.2.4. This confirms that we are not using these softwares, and moreover they are also in masked state. So, the files reported are not in use by the system at all" | dhcLinuxTemplateConfiguration.md | - | TBD |
| M700784_Preventing shutdown without login | - | - | All Linux Servers | Shutdown actions initiated by the hypervisor, cloud console, or cloud API do not require authentication or authorization from the client OS | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M447647_Secure Configuration Files | - | - | All Linux Servers | This measure can be skipped for selected services whenever the particular service requires write-access to configuration files for proper functionality. | CERT_UNIX_Linux_Measure_Plan. | - | TBD |
| M250305_Standard Virus Scanner | All Linux Servers | - | - | Agent is looking for services or files other than McAfee. Carry forwarded from DPC Siemens | - | - | TBD |
| M447203_Special Virus Scanner | - | - | All Linux Servers | Agent is looking for services or files other than McAfee. Carry forwarded from DPC Siemens | - | - | TBD |
| M202900_Check active Network Connections | All Linux Servers | - | - | Firewall controls network ports and allows only approved ports to communicate | DHC-3269 | - | TBD |
| M910080_Concept of Least Privileges for Users | - | All Linux Servers | - | The sudoers file is required for automation as per the existing configure ubuntu compliance playbook | - | - | TBD |
| M505890_Secure SSH Server | All Linux Servers | - | - | Partially Remediated. Part A - looking for openssh, which is already installed. Part B - permitting root login, this is remediated. | DHC-3281 | in progress | TBD |
| M410802_Protecting Web Server | - | - | All Linux Servers | Management stacks do not have web server installed. | - | - | TBD |

#### 2.1.3 vSphere CERT

The detailed description for each vSphere CERT Measure is available in [vSphere CERT](files/lldSiemensCERTExceptions/cERTVMwareVSphere6MeasurePlan.pdf)

| Measure No. | Exception | False Positives | Not Applicable | Reason | Source | Risk And Mitigation | Approval |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M135290_Compliance with Corporate Password Policy | ESXi/vCenter | - | - | VCS is following password polcy for vCenter SSO as mentioned in this measure, so its already compliant. For ESXi host, vCF 4.2.1 by default support 20 charecters which will be available from VCS 1.4. Need exception earlier release of VCS 1.4 for this policy as previous version of vCF suppost 15 charecters for password rotation. | DHC-3855 | - | TBD |
| M135278_Use Siemens PKI Certificates | ESXi/vCenter | - | - | This is not in VCS Scope. | DHC-3525 | - | TBD |
| M135612_Enable Persistent Logging | ESXi/vCenter | - | - | The syslog directory is pointing to /scratch partition which in turn is not a ramdisk or non-persistent location. The scratch is located on a disk attached vmfs extent which does not participate in vsan. | DHC-3277 /DHC-1962 | - | TBD |
| M135833_Separate Management and User Networks | ESXi/vCenter | - | - | Design Limitation | DHC-3854 | - | TBD |
| M135449_Check Network Isolation at Regular Intervals | ESXi/vCenter | - | - | Design Limitation | DHC-3854 | - | TBD |
| M135550_Disable Native VLAN Feature for All Switches | ESXi/vCenter | - | - | This is not in VCS Scope. | DHC-3854 | - | TBD |
| M135363_Enable Network File Copy Encryption | - | - | ESXi/vCenter | The parameter config.nfc.useSSL was applicable only for the desktop vSphere Client, which was discontinued in vSphere 6.5. All NFC connections now use SSL. This is not applicable for vSphere7. | DHC-3525 | - | TBD |
| M135444_Secure vCenter Single Sign On | - | - | ESXi/vCenter | This is not applicable for vSphere7 | DHC-3857 | - | TBD |
| M135895_Do Not Use the Root User | ESXi/vCenter | - | - | There are other playbooks using Root | DHC-3271 | - | TBD |
| M135481_Secure Storage Traffic | - | - | ESXi/vCenter | This measure rules to be followed and do not need any development | DHC-3857 | - | TBD |
| M135782_Patching of Inactive Virtual Machines and Templates | - | - | ESXi/vCenter | This measure rules to be followed and do not need any development | DHC-3857 | - | TBD |
| M135504_Secure Update Manager | - | - | ESXi/vCenter | VCS is using SDDC but not update manager | DHC-3857 | - | TBD |
