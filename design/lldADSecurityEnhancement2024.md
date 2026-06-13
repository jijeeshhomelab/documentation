# Security Measure Exceptions

## Table of Contents

## Changelog

| Date       | TOS     | Issue                       | Author                | Description                         |
| ---------- | ------- | --------------------------- | --------------------- | ----------------------------------- |
| 12.03.2024 | VCS 1.8 | VCS-12028                   | Sebastian Pucek | Initial document creation                 |
| 12.05.2025 | VCS 2.1 | VCS-15645                   | Ciprian Sferle  | Extend dhcAdSecurityEnhancement document  |
| 02.06.2025 | VCS 2.2 | VCS-16194                   | Ciprian Sferle  | Nessus - High #42873 SSL Medium Strength Cipher Suites Supported (SWEET32) |
| 24.02.2026 | VCS 2.2 | VCS-15538                   | Przemyslaw Pakula     | Add Security Requirements Coverage        |

# 1 Introduction

## 1.1 Purpose

The purpose of this document is to provide information on how to enhance security in VCS's AD.

## 1.2 Audience

This document is intended for Atos Cloud Services Engineers and Architects responsible for VMware Cloud Services (VCS) solution implementation and maintenance.

## 1.3 Related documents

### Security Requirements Coverage

| Instruction Name | Short Description |
| :----------: | ------- |
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

## 2 List of Vulnerabilities

### C1 Unsecured Configuration of Netlogon Protocol

| Measure ID | C1 |
| --- | --- |
| Tenable Link | [Unsecured Configuration of Netlogon Protocol](https://tenable-ad.saacon.net/profile/atos_asn/indicators-of-exposure/details/45-C-NETLOGON-SECURITY/information) |
| Affected Area | VCS Domain Controllers. |
| Summary | Domain Controller in VCS domain not compliant with recommended settings from a 2021 patch. |
| Remediation Implemented | The registry key that forces secure RPC calls for the Netlogon protocol should be applied on all DCs in the forest. |
| Notes | - |

### C6 ADCS Dangerous Misconfigurations

| Measure ID | C6 |
| --- | --- |
| Tenable Link | [ADCS Dangerous Misconfigurations](https://tenable-ad.saacon.net/profile/atos_asn/indicators-of-exposure/details/48-C-PKI-DANG-ACCESS/information) |
| Affected Area | Certificate Template in VCS. |
| Summary | Unsafe permissions used on certificate template: **S-1-5-21-319152546-1057728842-2186071682-515 (p24dhc02.next\Domain Computers) - Has "all extended rights"** "CN=WinRM SSL,CN=Certificate Templates,CN=Public Key Services,CN=Services,CN=Configuration,DC=p24dhc02,DC=next" |
| Remediation Implemented | This fix restricts access to the WinRM SSL certificate template to a limited number of computers. |
| Notes | Trusted computers are assigned to group the **rsce-dhc-ad-g-trustedcomputers**. This fix requires manual re-enrollment of the certificates. |

### C8 Application of Weak Password Policies on Users

| Measure ID | C8 |
| --- | --- |
| Tenable Link | [Application of Weak Password Policies on Users](https://tenable-ad.saacon.net/profile/atos_asn/indicators-of-exposure/details/29-C-PASSWORD-POLICY/information) |
| Affected Area | Password Policy. |
| Summary | Tenable states that there should be separate Password Setting Objects (PSO) and GPO password policies as GPO policies "are set globally and 'can' be insecure if weekly set". It is recommended to achieve this at the user group level. |
| Remediation Implemented | Create and assign PSO to each role in VCS. |
| Notes | The fix implements PSO for each role in AD. |

### C10 Dangerous Kerberos Delegation

| Measure ID | C10 |
| --- | --- |
| Tenable Link | [Dangerous Kerberos Delegation](https://tenable-ad.saacon.net/profile/atos_asn/indicators-of-exposure/details/13-C-UNCONST-DELEG/information) |
| Affected Area | Multiple Operational Users. |
| Summary | Current settings allow "Trusted for Delegation" on VCS management servers. Best practice is to only allow this on Domain controllers. As it is, many users can be trusted across multiple servers within the management stack. |
| Remediation Implemented | This fix set properties of administrators' accounts "Account is sensitive and cannot be delegated" to true. |
| Notes | Due to the vulnerability reported, it is also advised to set the NOT_DELEGATE User Account Control flag to the ica001 AD computer object. |

### C12 Native Administrative Group Members

| Measure ID | C12 |
| --- | --- |
| Tenable Link | [Native Administrative Group Members](https://tenable-ad.saacon.net/profile/atos_asn/indicators-of-exposure/details/8-C-NATIVE-ADM-GROUP-MEMBERS/information) |
| Affected Area | Multiple Operational Users. |
| Summary | There are too many members in the "Domain Admins" group. Leads to a high risk of secret theft. |
| Remediation Implemented | Restrict the number of Domain Admins group members. |
| Notes | This fix could not be fully automated. Require manual steps to delegate special privileges to groups and roles. |

### H15 Protected Users Group not Used

| Measure ID | H15 |
| --- | --- |
| Tenable Link | [Protected Users Group not Used](https://tenable-ad.saacon.net/profile/atos_asn/indicators-of-exposure/details/38-C-PROTECTED-USERS-GROUP-UNUSED/information) |
| Affected Area | VCS Privileged Users. |
| Summary | Since W20212R2, this group has been available. It is not used in VCS. It has many positive and no negative effects. |
| Remediation Implemented | Add the Domain Admins group to the Protected Users group. |
| Notes | It is a fully automated playbook. |

### H16 Logon Restrictions for Privileged Users

| Measure ID | H16 |
| --- | --- |
| Tenable Link | [Logon Restrictions for Privileged Users](https://tenable-ad.saacon.net/profile/atos_asn/indicators-of-exposure/details/46-C-ADMIN-RESTRICT-AUTH/information) |
| Affected Area | VCS Privileged Users. |
| Summary | There is a lack of GPO for restricting the authentication of privileged users on lower-tier (untrusted) machines. |
| Remediation Implemented | Change GPO to only allow privileged users to log on to TRUSTED machines. |
| Notes | - |

### O1 Accounts with adminCount attribute

| Measure ID | O1 |
| --- | --- |
| ORADAD scan ID | [vuln_dont_expire](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_dont_expire) |
| Affected Area | VCS Users |
| Summary | When an account leaves all privileged groups, the adminCount attribute remains set to 1, and permission inheritance remains disabled. |
| Remediation Implemented | Set adminCount attribute for privileged accounts to 0 |
| Notes | - |

### O2 Accounts with never-expiring passwords

| Measure ID | O2 |
| --- | --- |
| ORADAD scan ID | [warn_admincount](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#warn_admincount) |
| Affected Area | VCS Users |
| Summary | Account recovery allows a malicious individual to retain their access rights to the domain in the long term. |
| Remediation Implemented | Set PasseordNeverExpires attribute to False |
| Notes | - |

### O3 Built-in administrator accounts have been used in the past 30 days

| Measure ID | O3 |
| --- | --- |
| ORADAD scan ID | [warn_rid500](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#warn_rid500) |
| Affected Area | VCS Active Directory |
| Summary | The "Built-in Administrator" account (RID 500) is completely exempt from certain security policies. This allows, as a last resort, to use this account to correct a possible configuration error. This account has a "window-breaking" role and should never be used daily. |
| Remediation Implemented | Alert definer in Aria Log Insight and Aria Operations. |
| Notes | - |

### O4 Dangerous dSHeuristics settings

| Measure ID | O4 |
| --- | --- |
| ORADAD scan ID | [vuln_dsheuristics_bad](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_dsheuristics_bad) |
| Affected Area | VCS Active Directory |
| Summary | Dangerous parameters are configured in the dSHeuristics attribute |
| Remediation Implemented | dSHeuristics attribute set to 00000000010000000002000000011 |
| Notes | - |

### O5 DC/RODC supported encryption algorithms

| Measure ID | O5 |
| --- | --- |
| ORADAD scan ID | [vuln_dc_crypto](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_dc_crypto) |
| Affected Area | VCS Active Directory |
| Summary | DC/RODCs implement different encryption algorithms for Kerberos tickets (DES, RC4, AES). Initially, only the RC4 algorithm (RC4-HMAC) was supported for Windows systems, and DES-based algorithms could be enabled to improve compatibility with non-Windows systems. |
| Remediation Implemented | Change GPO policy Network security: Configure encryption types allowed for Kerberos to allow only AES128-CTS-HMAC-SHA1-96, AES256-CTS-HMAC-SHA1-96 and "Future encryption types" encryption types |
| Notes | - |

### O6 Incorrect object owners

| Measure ID | O6 |
| --- | --- |
| ORADAD scan ID | [vuln_owner](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_owner) |
| Affected Area | VCS Active Directory |
| Summary | Items created more than 7 days ago have non-standard owners (not owned by the Domain Admins group). |
| Remediation Implemented | Change owner to the Domain Admin group. |
| Notes | - |

### O7 Krbtgt account password unchanged for more than a year

| Measure ID | O7 |
| --- | --- |
| ORADAD scan ID | [vuln_krbtgt](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_krbtgt) |
| Affected Area | VCS Active Directory |
| Summary | The krbtgt account password has not been changed for more than a year. |
| Remediation Implemented | Reset password for the Kerberos user account based on a monthly schedule |
| Notes | - |

### O8 Privileged accounts outside of the Protected Users group

| Measure ID | O8 |
| --- | --- |
| ORADAD scan ID | [vuln_protected_users](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_protected_users) |
| Affected Area | VCS Priviledged Users |
| Summary | Some privileged accounts are not protected by the Protected Users group. |
| Remediation Implemented | Add privileged user accounts to the Protected Users group (already performed by H16 fix). |
| Notes | - |

### O9 Privileged accounts with never-expiring passwords

| Measure ID | O9 |
| --- | --- |
| ORADAD scan ID | [vuln_dont_expire_priv](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_dont_expire_priv) |
| Affected Area | VCS Priviledged Users |
| Summary | Some privileged accounts have passwords that do not expire |
| Remediation Implemented | Set PasseordNeverExpires attribute to False (already performed by O2 fix) |
| Notes | - |

### O10 Privileged group members not in an authentication silo

| Measure ID | O10 |
| --- | --- |
| ORADAD scan ID | [vuln_silo_priv](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_silo_priv) |
| Affected Area | VCS Priviledged Users |
| Summary | Members of privileged groups are not members of an authentication silo, or the silo is not configured correctly. |
| Remediation Implemented | Privledged user accounts are added to Authentication Policy Silos. |
| Notes | - |

### O11 Servers with passwords unchanged for more than 45 days

| Measure ID | O11 |
| --- | --- |
| ORADAD scan ID | [vuln_password_change_server_no_change_45](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_password_change_server_no_change_45) |
| Affected Area | VCS Computer Accounts |
| Summary | Servers have not changed their computer account passwords for more than 45 days, indicating that their secrets are not being renewed. |
| Remediation Implemented | Change GPO policy value for Domain member: Disable machine account password changes to Disabled, and for policy Domain member: Maximum machine account password age to 30 days value. |
| Notes | - |

### O12 Service accounts supported encryption algorithms

| Measure ID | O12 |
| --- | --- |
| ORADAD scan ID | [vuln_kerberos_properties_encryption](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_kerberos_properties_encryption) |
| Affected Area | VCS Active Directory |
| Summary | Service accounts that support Kerberos authentication (i.e., have a servicePrincipalName attribute) can use different encryption algorithms (DES, RC4, AES128, and AES256) for tickets issued to services running under the identity of those accounts. |
| Remediation Implemented | Set computer account msDS-SupportedEncryptionTypes attribute to decimal value 24, which is equal to 0x18=(AES128_CTS_HMAC_SHA1_96 &#124; AES128_CTS_HMAC_SHA1_96). |
| Notes | - |

### O13 Unrestricted domain join

| Measure ID | O13 |
| --- | --- |
| ORADAD scan ID | [vuln_user_accounts_machineaccountquota](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_user_accounts_machineaccountquota) |
| Affected Area | VCS Computer Accounts |
| Summary | By default, authenticated users can join 10 machines to the domain. This value is controlled by the ms-DS-MachineAccountQuota attribute of the domain root. |
| Remediation Implemented | Set the ms-DS-MachineAccountQuota attribute to 0 and grant platform administrators the privilege to add computer accounts to the domain |
| Notes | - |

### O14 Use of the "Pre-Windows 2000 Compatible Access" group

| Measure ID | O14 |
| --- | --- |
| ORADAD scan ID | [vuln_compatible_2000_not_default](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_compatible_2000_not_default) |
| Affected Area | VCS Users |
| Summary | The pre-Windows 2000 compatibility mechanism is enabled for some accounts. This mechanism allows access to certain RPCs to group members, BuiltIn\Pre-Windows 2000 Compatible Access. |
| Remediation Implemented | Authenticated Users group is added to the Pre-Windows 2000 Compatible Access group. |
| Notes | - |

### O15 Dangerous permissions on the DnsAdmins group

| Measure ID | D15 |
| --- | --- |
| ORADAD scan ID | [vuln_dnsadmins](https://www.cert.ssi.gouv.fr/uploads/ad_checklist.html#vuln_dnsadmins) |
| Affected Area | VCS DNS Management |
| Summary | The DnsAdmins group includes some non-privileged member accounts or accounts that have dangerous permissions set. |
| Remediation Implemented | Remove users from the DnsAdmins group and create a dedicated limited privileges group for DNS management activities. |
| Notes | - |
