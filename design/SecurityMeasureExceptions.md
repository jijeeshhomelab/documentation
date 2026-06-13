# Security Measure Exceptions

## Changelog

| Date       | TOS     | Issue                       | Author                | Description                                                       |
| ---------- | ------- | --------------------------- | --------------------- | ----------------------------------------------------------------- |
| 15.07.2021 | VCS 1.3 | DHC-2355                    | Kathirvel Krishnasamy | Initial document creation                                         |
| 23.02.2022 | VCS 1.4 | DHC-4209                    | Kathirvel Krishnasamy | Updated 2.1 Alcatraz and 2.2 Nessus                               |
| 20.04.2022 | VCS 1.4 | DHC-4479                    | Kathirvel Krishnasamy | Updated 2.1 Alcatraz and 2.2 Nessus                               |
| 14.06.2022 | VCS 1.4 | DHC-4988                    | Kathirvel Krishnasamy | Updated 2.1 Alcatraz                                              |
| 04.08.2022 | VCS 1.4 | VCS-298                     | Sachin Choudhary      | updated 2.2 Nessus                                                |
| 10.08.2022 | VCS 1.4 | VCS-606                     | Kathirvel Krishnasamy | Updated 2.2 Nessus                                                |
| 08.09.2022 | VCS 1.5 | VCS-609                     | Kathirvel Krishnasamy | Updated 2.2 Nessus                                                |
| 10.10.2022 | VCS 1.6 | VCS-4255                    | Kathirvel Krishnasamy | Updated 2.2 Nessus                                                |
| 15.11.2022 | VCS 1.6 | VCS-4963 and VCS-4723       | Kathirvel Krishnasamy | Updated 2.1 Alcatraz and 2.2 Nessus                               |
| 21-12-2022 | VCS 1.6 | VCS-5378                    | Abhishek Sawant       | Updated 2.1 Alcatraz                                              |
| 02-01-2023 | VCS 1.6 | VCS-5410                    | Kathirvel Krishnasamy | Updated 2.1 Alcatraz                                              |
| 07-06-2023 | VCS 1.7 | VCS-9755                    | Shilpa Arote          | Updated 2.2 Nessus                                                |
| 27-03-2025 | VCS 2.0 | VCS-14130                   | Mariusz Stanek        | Update for VCS 2.0                                                |
| 02-06-2025 | VCS 2.2 | VCS-14166                   | Radoslaw Dabrowski    | Update for VCS 2.2 and expand with exception reasoning            |
| 20-06-2025 | VCS 2.2 | VCS-16335                   | Divyapraksh J         | Update details about CVE-2024-6387 from Nessus Scan               |
| 30-06-2025 | VCS 2.2 | VCS-16675                   | Tomasz Korniluk       | Update for VCS 2.2 and expand with exception reasoning            |
| 01-07-2025 | VCS 2.2 | VCS-16721                   | Tomasz Korniluk       | Update for VCS 2.2, added new chapter for scanner mitigation list |
| 24-02-2026 | VCS 2.2 | VCS-15538                   | Przemyslaw Pakula     | Added Security Requirements Coverage                              |

# 1 Introduction

## 1.1 Purpose

The purpose of this document is to provide the information of Nessus/Alcatraz exception and false positives.

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

## 2 List of Exception and False Positives

### 2.1 Alcatraz

## Alcatraz Exception and False Positives List

- **Measure ID:** `WI00007_Configure_a_unique_binding`
  - **Description:** Configure a unique binding for the protocols http, https and ftp only to the interfaces and IP addresses which it should listen to.
  - **Exception:** ICA001 and WUS001
  - **False Positives:** N/A
  - **Reason:** The IIS Server Console fails to load when strict IP bindings are applied. This is due to the IIS Manager requiring a default binding to `All Unassigned` interfaces for proper initialization. Restricting bindings to specific IPs causes the console to become inaccessible, which disrupts administrative operations.
  - **Source:** <https://atos365.sharepoint.com/sites/100000120/PublishedStorage/Forms/All%20Documents%202.aspx?id=%2Fsites%2F100000120%2FPublishedStorage%2FTechnical%20Security%20Specifications%20for%20VMware%20Tools%2Epdf&parent=%2Fsites%2F100000120%2FPublishedStorage>

- **Measure ID:** `Web-IIS_V6.0_WI00003_Storage-Location`  
  - **Description:** Store the web content in a dedicated NTFS disk volume that does not contain the operating system.  
  - **Exception:** ICA001 and WUS001  
  - **False Positives:** N/A  
  - **Reason:** The WSUS server is currently provisioned with two virtual disks, which allows for potential separation of update content from the operating system volume. However, the update content remains on the system drive due to legacy configuration and operational continuity. The ICA server is provisioned with only a single virtual disk, making separation of web content from the OS volume technically infeasible without rearchitecting the deployment.  
  - **Source:** Internal infrastructure validation and current VM provisioning state.

- **Measure ID:** `Web-IIS_V6.0_WI00015_.NET-Trust-Level`  
  - **Description:** ASP.NET trust level to be set to medium  
  - **Exception:** ICA001 and WUS001  
  - **False Positives:** N/A  
  - **Reason:** The IIS Console fails to load or operate correctly when the ASP.NET trust level is set to "medium". This behavior is observed in environments where administrative tools or WSUS components require full trust to function. The default trust level in IIS is "Full", and changing it to "Medium" can break compatibility with certain .NET components or legacy configurations.  
  - **Source:** Confirmed in [Technical Security Specifications for Web IIS](https://atos365.sharepoint.com/sites/100000120/PublishedStorage/Technical%20Security%20Specifications%20for%20Web%20IIS.pdf?web=1)

- **Measure ID:** `Web-IIS_V6.0_WI00017_NTFS-Auditing-Settings`
  - **Description:** Configure NTFS auditing for the web content folder
  - **Exception:** ICA001 and WUS001
  - **False Positives:** Tool reports missing SACL permissions (e.g., `FILE_EXECUTE`, `FILE_WRITE_DATA`) on directories like `C:\inetpub\wwwroot`, despite auditing being enabled
  - **Reason:** NTFS auditing is enabled and functioning. Manual validation confirms audit entries are being generated. The compliance tool fails to detect inherited or advanced SACL configurations, resulting in false negatives.
  - **Source:** Originated from DPC baseline, official documentation or technical justification not found

- **Measure ID:** `Web-IIS_V6.0_WI00021_Disable-weak-hashing-algorithms`
  - **Description:** Disable weak hashing algorithms to enhance cryptographic security
  - **Exception:** ICA001 and WUS001
  - **False Positives:** N/A
  - **Reason:** Post-remediation, Remote Desktop Protocol (RDP) fails to connect due to incompatibility with legacy systems relying on deprecated hashing algorithms. This is a compatibility constraint, not a misconfiguration.
  - **Source:** Originated from DPC baseline, official documentation or technical justification not found

- **Measure ID:** `Web-IIS_V6.0_WI00023_IIS-logging`
  - **Description:** Enable IIS logging in W3C format, select all fields on site level and schedule logfile rollover. Ensure storing of logs in a secured location with access rights only for authorized persons and systems. Remove all logs which exceeding retention time of the national law.
  - **Exception:** ICA001 and WUS001
  - **False Positives:** N/A
  - **Reason:** Full compliance could not be achieved due to limitations in the available IIS modules or configuration constraints. Specific fields or rollover settings are not supported in the current deployment. Logging is active and logs are stored securely.
  - **Source:** Originated from DPC baseline, official documentation or technical justification not found

- **Measure ID:** `Web-IIS_V6.0_WI00026_X-Frame-Options`
  - **Description:** To protect Web Applications against ClickJacking.
  - **Exception:** ICA001 and WUS001
  - **False Positives:** N/A
  - **Reason:** Enabling the X-Frame-Options header caused the IIS Web Console to become inaccessible. This behavior was observed during implementation and confirmed across multiple environments. The setting conflicts with the way certain administrative interfaces are rendered in frames.
  - **Source:** Originated from DPC baseline, official documentation or technical justification not found

- **Measure ID:** `Server-Windows_V5.1_SW00001_Authorization-Administrative-Delegation`
  - **Description:** Enable computer and user accounts to be trusted for delegation
  - **Exception:** Domain Controllers
  - **False Positives:** N/A
  - **Reason:** This measure is applicable to all servers except domain controllers. On domain controllers, the setting must not be left empty and must include "Administrators" as per operational requirements. Applying the standard configuration would conflict with domain controller role-specific delegation policies.
  - **Source:** Originated from DPC baseline, official documentation or technical justification not found

- **Measure ID:** `Server-Windows_V5.1_SW00038_Screen-Saver-Enabled`
  - **Description:** The screen saver must be enabled
  - **Exception:** N/A
  - **False Positives:** All Windows Servers
  - **Reason:** When the compliance agent is executed remotely, it fails to detect the screen saver setting correctly, resulting in false non-compliance. However, when the same scan is run locally on the server, the setting is detected as compliant. This discrepancy is due to how Group Policy settings are applied and queried remotely.
  - **Source:** Originated from DPC baseline, referenced in Jira ticket VCS-16675, official documentation or technical justification not found.

- **Measure ID:** `Server-Windows_V5.1_SW00039_Screen-Saver-Password-Protected`
  - **Description:** The screen saver must be password protected
  - **Exception:** N/A
  - **False Positives:** All Windows Servers
  - **Reason:** When the compliance agent is executed remotely, it fails to detect the screen saver password protection setting correctly, resulting in false non-compliance. However, when the same scan is run locally on the server, the setting is detected as compliant. This discrepancy is due to how Group Policy settings are applied and queried remotely.
  - **Source:** Originated from DPC baseline, referenced in Jira ticket VCS-16675, official documentation or technical justification not found.

- **Measure ID:** `Server-Unix_V7.0_3SU00028_Disable-R-Daemons-And-Telnet-Daemon`
  - **Description:** All remote management daemons (chargen, cmsd, comsat, ftp, etc) must be disabled. DNS Print spooler daemons and bind must not run, except on systems where required for service delivery
  - **Exception:** SRS001
  - **False Positives:** N/A
  - **Reason:** Disabling the required services as per the measure caused the Postfix mail service to stop functioning. Postfix relies on certain daemon bindings (e.g., DNS or localhost-bound ports) that are flagged by the compliance tool, even though they are essential for mail delivery and do not pose a security risk in the current configuration.
  - **Source:** Originated from DPC baseline, official documentation or technical justification not found

- **Measure ID:** `Server-Unix_V8.0_2SU00050_World-Writable-OS-Directories-Must-Have-The-Sticky-Bit-Set`
  - **Description:** OS directories must be protected by unauthorized users.
  - **Exception:** N/A
  - **False Positives:** ANS001
  - **Reason:** The remediation task is unable to proceed due to file path restrictions. In several environments, the affected paths (e.g., `/etc/vmware-tools/locations.lck`, `/tmp/add_remote_user.*`) are either dynamically generated or managed by third-party applications, making it technically infeasible to apply sticky bit settings without risking service disruption.
  - **Source:** Official documentation or technical justification not found

- **Measure ID:** `Server-Unix_V8.0_2SU00056_SSH-Kex-Algorithm`
  - **Description:** This variable limits the types of Kex algorithms that SSH can use during communication.
  - **Exception:** N/A
  - **False Positives:** All
  - **Reason:** The `KexAlgorithms` parameter was modified in accordance with the security baseline, but the compliance scan continues to report the system as non-compliant. This is likely due to the scanner not correctly parsing the updated configuration or not recognizing the applied changes in certain OS variants.
  - **Source:** Justification provided via internal email communication, no formal documentation found

- **Measure ID:** `Server-Unix_V8.0_3SU00041_Max-Login-Retries`
  - **Description:** Accounts must include lockout, after at least 5 incorrect attempts, and at most 10 incorrect attempts. Auto enabling is allowed after 30 minutes.
  - **Exception:** N/A
  - **False Positives:** All
  - **Reason:** The retry interval is configured to six, which is technically compliant, but the compliance tool flags it as non-compliant due to misinterpretation or misalignment with the expected configuration syntax. Manual validation confirms the setting is in place.
  - **Source:** Justification provided via internal email communication, no formal documentation found

- **Measure ID:** `Server-Unix_V7.0_3SU00009_Monitor-System-Files-For-Ownership-And-Permissions`
  - **Description:** Certain system files and directories need to be monitored to ensure changes do not affect the stability of the system. Incorrect protection and ownership can lead to loss of functionality (if too strict) and security exposure (if too permissive). Do not change the vendor-supplied settings unless there are compelling reasons to do so.
  - **Exception:** N/A
  - **False Positives:** All
  - **Reason:** Remediation was applied successfully, but the changes revert after system reboot due to OS-level or package-specific behavior. This is commonly observed in environments where configuration files are regenerated or overwritten by system services or automation tools.
  - **Source:** Justification provided via internal email communication; no formal documentation found

- **Measure ID:** `Server-Unix_V7.0_3WA00002_Apache-Modules-Activation`
  - **Description:** Many modules can be installed with Apache and not all of them are useful for a given scope. Activating only needed components reduces the attack surface.
  - **Exception:** N/A
  - **False Positives:** DEB001
  - **Reason:** Disabling this module stops the listing of directories on the web page, which is required for patching servers. This behavior was observed during testing and confirmed as necessary for operational continuity.
  - **Source:** Referenced in Jira ticket VCS-3850, no formal technical justification found

- **Measure ID:** `Server-Unix_V7.0_3WA00003_System-User-Management`
  - **Description:** Running Apache under an account with too many privileges may allow an attacker to gain control of the server and perform important damages.
  - **Exception:** N/A
  - **False Positives:** DEB001
  - **Reason:** The Apache service is already running under the local user `www-data`, and login is disabled for this user. This configuration meets the intent of the control, but the tool flags it due to a mismatch in expected user naming or privilege detection.
  - **Source:** Referenced in Jira ticket VCS-3850, no formal technical justification found

- **Measure ID:** `Server-Unix_V7.0_3WA00007_Apache-Logging`
  - **Description:** Log files are a critical element to ensure that both the server is behaving as expected and trace are left whenever some attacks are attempted on the web server.
  - **Exception:** N/A
  - **False Positives:** DEB001
  - **Reason:** The Apache logging configuration was remediated in accordance with the TSS document, but the compliance scan continues to report it as non-compliant. This is likely due to the scanner not recognizing the applied configuration or expecting a different syntax or path.
  - **Source:** Referenced in Jira ticket VCS-3850, no formal technical justification found

- **Measure ID:** `2VV00005 - Disable TLSv1.0, TLSv1.1`
  - **Description:** TLS v 1.0, 1.1 are insecure and need to be disabled
  - **Exception:** N/A
  - **False Positives:** N/A
  - **Reason:** The VMware team confirmed that while TLS 1.0 and 1.1 have been disabled as per the remediation guidelines, the compliance tool (TOSCA) does not validate TLS protocol versions directly. It only checks certificate validity, which leads to a persistent non-compliant status despite correct implementation.
  - **Source:** Originated from DPC baseline, official documentation or technical justification not found

- **Measure ID:** `3VV00006 - Set Admin account policy`
  - **Description:** Set the password policy for the admin account
  - **Exception:** N/A
  - **False Positives:** N/A
  - **Reason:** The remediation has been applied in accordance with the TSS, but the compliance tool (TOSCA) does not validate the presence or configuration of the admin password policy. It only checks for certificate validity or other unrelated indicators, resulting in a persistent non-compliant status despite correct implementation.
  - **Source:** Originated from DPC baseline, official documentation or technical justification not found

- **Measure ID:** `1VE00012 Securely Configure Persistent Logging For All ESXi 6.x and 7.0 Hosts`
  - **Description:** The logs contain configuration and infrastructure details, such as IP and MAC addresses. These should be secured to prevent information disclosure, which could constitute a data protection violation.
  - **Exception:** ESXi
  - **False Positives:** N/A
  - **Reason:** The required logging configuration could not be applied due to limitations in Aria Operations (vROps). Although the remediation is technically implemented, the tool does not support validation of this setting, resulting in persistent non-compliance in scan results.
  - **Source:** Justification provided via internal email communication, no formal documentation found

## Alcatraz scanner measures mitigatation list

Chapter list the measures to mitigate inside the scanner (false positives or exceptions).

- **Measure ID:** `Server-Windows_V5.1_SW00038_Screen-Saver-Enabled`
  - **Description:** The screen saver must be enabled
  - **Scope**: All Windows Servers
  - **Mitigation:** Update Alcatraz scanner measure rule remove part for policy checks, only use registry task checks.
  - **Source:** Jira ticket VCS-16675

- **Measure ID:** `Server-Windows_V5.1_SW00039_Screen-Saver-Password-Protected`
  - **Description:** The screen saver must be password protected
  - **Scope**: All Windows Servers
  - **Mitigation:** Update Alcatraz scanner measure rule remove part for policy checks, only use registry task checks.
  - **Source:** Jira ticket VCS-16675

- **Measure ID:** `Web-IIS_V6.0_WI00023_IIS-logging`
  - **Description:** Enable IIS logging in W3C format, select all fields on site level and schedule logfile rollover. Ensure storing of logs in a secured location with access rights only for authorized persons and systems. Remove all logs which exceeding retention time of the national law.
  - **Scope**: All Windows IIS Servers
  - **Mitigation:** Apply the below IIS log settings under application conf. file inside section "Default Web Site":

  ```yaml
  <logFile logExtFileFlags="Date, Time, ClientIP, ComputerName, UserName, SiteName, ServerIP, Method, Cookie, UriStem, UriQuery, HttpStatus, Win32Status, TimeTaken, ServerPort, UserAgent, Referer, HttpSubStatus, Host, ProtocolVersion, BytesSent, BytesRecv" />
  ```

  - **Source:** Jira ticket VCS-16626

- **Measure ID:** `Web-IIS_V6.0_WI00026_X-Frame-Options`
  - **Description:** To protect Web Applications against ClickJacking.
  - **Scope**: All Windows IIS Servers
  - **Mitigation:** Apply the below IIS cli command to enable X-Frame Option for the "Default Web Site"

   ```yaml
      %windir%\system32\inetsrv\appcmd set config "Default Web Site" -section:system.webServer/httpProtocol /+"customHeaders.[name='DENY',value='Local']" /commit:apphost
   ```

  - **Source:** Jira ticket VCS-16626

- **Measure ID:** `Web-IIS_V6.0_WI00003_Storage-Location`
  - **Description:** Store the web content in a dedicated NTFS disk volume that does not contain the operating system.
  - **Scope**: All Windows IIS Servers
  - **Mitigation:**
  
    ```yaml
    ## Create dedicated folder for CertEnroll web app
    New-Item -Path "C:\WebContent\CertEnroll" -ItemType Directory -Force
    ## Copy the web app content
    Copy-Item -Path "C:\windows\system32\CertSrv\CertEnroll*" -Destination "C:\WebContent\CertEnroll" -Recurse -Force
    ## Update the virtual directory in IIS
    Set-ItemProperty "IIS:\Sites\Default Web Site\CertEnroll" -Name physicalPath -Value "C:\WebContent\CertEnroll"
    ## Verify the new path
    Get-ItemProperty "IIS:\Sites\Default Web Site\CertEnroll" | Select-Object physicalPath
    ## Restart IIS
    iisreset
    ## Access the virtual directory in a browser
    http://<server>/CertEnroll/ or https://<server>/CertEnroll/
    ## After verification, disable directory browsing:
    Set-WebConfigurationProperty -Filter "/system.webServer/directoryBrowse" -PSPath "IIS:\Sites\Default Web Site" -Name "enabled" -Value "false"
    ```
  
  - **Source:** Jira ticket VCS-16632

- **Measure ID:** `WI00007_Configure_a_unique_binding`
  - **Description:** Configure a unique binding for the protocols http, https and ftp only to the interfaces and IP addresses which it should listen to.
  - **Scope**: All Windows IIS Servers
  - **Mitigation:** Full compliance can be achivied executing IIS cli commands described inside Jira ticket VCS-16631

  ```yaml
  appcmd set site /site.name:"Default Web Site" /+bindings.[protocol='http',bindingInformation='<Server-IP>:80:<Server-FQDN>']
  appcmd set site /site.name:"Default Web Site" /+bindings.[protocol='https',bindingInformation='<Server-IP>:80:<Server-FQDN>']
  ### Bind SSL certificate
  netsh http add sslcert ipport=<Server-IP>:443 certhash=THUMBPRINT appid="{SSL-CERTIFICATE-ID}"
  ```
  
  - **Source:** Jira ticket VCS-16631
  
### 2.2 Nessus

- **Plugin ID:** `50686`
  - **Description:** IP Forwarding Enabled
  - **Exception:** Core Switch
  - **False Positives:** N/A
  - **Reason:** As per email received from Network Team, exclude this device (for all environments NX and VX) as it is not part of VCS stack and is simulating a few services including NTP and Gateway that will be managed by local NDCS teams in Production.
    - IP forwarding is enabled on this core switch as it is not part of the VCS stack but is instead simulating critical services such as NTP and Gateway functions. These services are essential for local network operations and are managed by the NDCS team in production. The switch also serves as a Layer 3 gateway, aggregating distribution and access layers, which necessitates IP forwarding to maintain routing functionality and service availability.
  - **Source:** Justification provided via internal email communication, no formal documentation found

- **Plugin ID:** `97861`
  - **Description:** Network Time Protocol (NTP) Mode 6 Scanner
  - **Exception:** Core Switch
  - **False Positives:** N/A
  - **Reason:** As per email received from Network Team, exclude this device (for all environments NX and VX) as it is not part of VCS stack and is simulating a few services including NTP and Gateway that will be managed by local NDCS teams in Production.
    - The core switch in question is not part of the VCS stack and is instead simulating essential services, including NTP and Gateway functionality. These services are critical for local network operations and are managed by the NDCS team in production. The NTP Mode 6 response is enabled to support diagnostic and synchronization tasks required by local infrastructure components. Disabling this functionality would disrupt time synchronization and monitoring capabilities for dependent systems.
  - **Source:** Justification provided via internal email communication, no formal documentation found

- **Plugin ID:** `146826`
  - **Description:** VMware vCenter Server 6.5 / 6.7 / 7.0 Multiple Vulnerabilities VMSA-2021-0002
  - **Exception:** Removed
  - **False Positives:** vCenter
  - **Reason:** This plugin corresponds to VMSA-2021-0002, which addresses two critical vulnerabilities:
    - **CVE-2021-21972** – Remote Code Execution (RCE) in the vSphere Client (HTML5)
    - **CVE-2021-21973** – Server-Side Request Forgery (SSRF) in the vSphere Client (HTML5)

    These vulnerabilities were fixed in the following versions:
    - vCenter Server 7.0 Update 1c (7.0 U1c)
    References:
    - VMware Advisory: [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)
    - Tenable Plugin: [146826](https://www.tenable.com/plugins/nessus/146826)
    - Broadcom Workaround Instructions: [KB 317865](https://knowledge.broadcom.com/external/article/317865)
  - **Source:** Confirmed in VCS 1.3, but remediated and no longer valid in later versions

- **Plugin ID:** `146826`
  - **Description:** VMware vCenter Server 6.5 / 6.7 / 7.0 Multiple Vulnerabilities VMSA-2021-0002
  - **Exception:** Removed
  - **False Positives:** vCenter
  - **Reason:** This vulnerability affects vCenter Server versions prior to 7.0 U1c. It includes CVE-2021-21972 (RCE) and CVE-2021-21973 (SSRF), which have been fully patched in 7.0 U1c and later.  
    References:
    - VMware Advisory: [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)
    - Tenable Plugin: [146826](https://www.tenable.com/plugins/nessus/146826)
    - Broadcom KB (Workaround): [317865](https://knowledge.broadcom.com/external/article/317865)
  - **Source:** Confirmed in VCS 1.3, but no longer applicable in later versions

- **Plugin ID:** `153889`
  - **Description:** VMware vCenter Server Arbitrary File Upload (VMSA-2021-0020)
  - **Exception:** Removed
  - **False Positives:** vCenter
  - **Reason:** This plugin corresponds to **CVE-2021-22005**, a critical arbitrary file upload vulnerability in the vCenter Server Analytics service. This vulnerability allows unauthenticated remote attackers with network access to port 443 to execute code on the vCenter Server by uploading a specially crafted file. The issue is resolved in **vCenter Server 7.0 Update 2c (7.0 U2c)** and later versions.

    References:
    - VMware Advisory: [VMSA-2021-0020](https://www.vmware.com/security/advisories/VMSA-2021-0020.html)
    - Tenable Plugin: [153889](https://www.tenable.com/plugins/nessus/153889)
    - Broadcom Workaround Instructions: [KB 85717](https://knowledge.broadcom.com/external/article?legacyId=85717)
  - **Source:** Confirmed in VCS 1.4, but no longer applicable in later versions

- **Plugin ID:** `153545`
  - **Description:** VMware vCenter Server < 7.0 U2c Multiple Vulnerabilities (VMSA-2021-0020)
  - **Exception:** Removed
  - **False Positives:** vCenter
  - **Reason:** This plugin encompasses multiple vulnerabilities, including **CVE-2021-22005** (arbitrary file upload), **CVE-2021-21991** (privilege escalation), and **CVE-2021-22006** (reverse proxy bypass). These issues are resolved in **vCenter Server 7.0 Update 2c (7.0 U2c)** and later versions.

    References:
    - VMware Advisory: [VMSA-2021-0020](https://www.vmware.com/security/advisories/VMSA-2021-0020.html)
    - Tenable Plugin: [153545](https://www.tenable.com/plugins/nessus/153545)
  - **Source:** Confirmed in VCS 1.4, but no longer applicable in later versions

- **Plugin ID:** `51192`
  - **Description:** SSL Certificate Cannot Be Trusted
  - **Exception:** All Management Devices (e.g., vROps, vRSLCM)
  - **False Positives:** N/A
  - **Reason:** This plugin is triggered when the server presents a certificate that is self-signed or not issued by a publicly trusted Certificate Authority (CA). In enterprise environments, tools like **ARIA Operations (vROps)** and **Aria Lifecycle Manager (vRSLCM)** commonly use self-signed certificates for internal-only communication over HTTPS.

    An exception was made based on the following justifications:

    - **Internal-Only Access:** These systems are not exposed to the public internet and are accessible only within a trusted management network.
    - **Secure Encryption Still In Place:** Even though the certificate is self-signed, SSL encryption is active, ensuring confidentiality and integrity of communications.
    - **Default Vendor Behavior:** VMware appliances like vROps and vRSLCM are deployed with self-signed certificates by default. Replacing them with publicly trusted certificates is optional and does not impact core functionality.
    - **Low Risk in Controlled Environment:** The likelihood of a man-in-the-middle (MITM) attack in this isolated network context is minimal due to firewalling, segmentation, or VPN access restrictions.
    - **Scanner Limitation:** Tools like Nessus identify untrusted certificates based on CA trust chains, not actual exposure or exploitability. This results in false positives for internal-only systems with self-signed certificates.

    References:
    - Tenable Plugin: [51192](https://www.tenable.com/plugins/nessus/51192)
    - Microsoft Discussion: [Remediating Nessus Plugin ID 51192](https://learn.microsoft.com/en-us/answers/questions/1195443/remediating-nessus-plugin-ids-51192-ssl-certificat)
    - VMware vRSLCM Cert Management Guide: [FunkyCloudMedina Blog](https://www.funkycloudmedina.com/2020/06/how-to-replace-vrealize-suite-lifecycle-manager-8.1-self-signed-certificate-with-a-certificate-from-your-microsoft-ca/)

  - **Source:** Manually reviewed and risk accepted for internal systems

- **Plugin ID:** `57582`
  - **Description:** SSL Self-Signed Certificate
  - **Exception:** All Management Devices (e.g., vROps, vRSLCM)
  - **False Positives:** N/A
  - **Reason:** This plugin flags SSL certificates that are self-signed and not issued by a trusted Certificate Authority (CA). In enterprise environments, it is common for internal management systems such as **vRealize Operations (vROps)** and **vRealize Suite Lifecycle Manager (vRSLCM)** to use self-signed certificates—especially during initial deployments or in non-production environments.

    An exception was made based on the following justifications:

    - **Internal Trust Model:** These systems operate exclusively within protected internal networks and are not exposed to the public internet.
    - **Encrypted Traffic Is Maintained:** Even though the certificate is self-signed, all communication is encrypted with TLS, ensuring secure internal communication.
    - **Default Deployment Behavior:** VMware products like vRSLCM and vROps ship with self-signed certificates by default. Replacing them with certificates from a public CA is optional and does not impact system functionality.
    - **Operational Practicality:** Using self-signed certificates avoids the complexity of external certificate lifecycle management, especially for services with no public exposure.
    - **False Positives by Design:** Scanners such as Nessus evaluate trust chains without considering deployment context. For internal-use services, this leads to expected false positives.

    References:
    - Tenable Plugin: [57582](https://www.tenable.com/plugins/nessus/57582)
    - VMware Docs: [Self-Signed Certificate Use](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-7F6F8E6B-53F7-4B4A-9A6D-C738A1C4310D.html)
    - Certificate Replacement Guide: [FunkyCloudMedina - vRSLCM Cert Replacement](https://www.funkycloudmedina.com/2020/06/how-to-replace-vrealize-suite-lifecycle-manager-8.1-self-signed-certificate-with-a-certificate-from-your-microsoft-ca/)

  - **Source:** Manually reviewed and risk accepted for internal systems

- **Plugin ID:** `156032`
  - **Description:** Apache Log4j Unsupported Version Detection
  - **Exception:** Removed
  - **False Positives:** N/A
  - **Reason:** This finding previously applied to End Point Operations (EPOps) agents, which utilized Apache Log4j 1.2.x. VMware confirmed that their EPOps implementation did not use the JMSAppender component, which is required to exploit CVE-2021-45046. However, the EPOps agents have since been fully decommissioned. The environment now uses **Telegraf agents**, which are written in **Go** and have no dependency on Java or Log4j. Therefore, this finding is no longer applicable and the exception has been removed.

    References:
    - VMware KB (EPOps and Log4j): [https://kb.vmware.com/s/article/87076](https://kb.vmware.com/s/article/87076)
    - Apache Log4j Vulnerability Details: [https://logging.apache.org/log4j/2.x/security.html](https://logging.apache.org/log4j/2.x/security.html)
    - Telegraf Source Code: [https://github.com/influxdata/telegraf](https://github.com/influxdata/telegraf)
    - InfluxData Log4j Statement: [https://www.influxdata.com/blog/apache-log4j-vulnerability-cve-2021-44228/](https://www.influxdata.com/blog/apache-log4j-vulnerability-cve-2021-44228/)
  - **Source:** Legacy detection, no longer valid after agent replacement

- **Plugin ID:** `156860`
  - **Description:** Apache Log4j 1.x Multiple Vulnerabilities
  - **Exception:** Removed
  - **False Positives:** N/A
  - **Reason:** This finding was previously justified due to the presence of Log4j 1.2.x in End Point Operations (EPOps) agents, which were configured in a way that avoided known vulnerable components (e.g., JMSAppender). As of now, the environment no longer uses EPOps agents and has transitioned to **Telegraf agents**, which are written in Go and have no dependency on Log4j. Therefore, this plugin is no longer applicable and the exception is removed.

    References:
    - CVE-2021-4104: [https://logging.apache.org/log4j/2.x/security.html](https://logging.apache.org/log4j/2.x/security.html)
    - Telegraf source and architecture: [https://github.com/influxdata/telegraf](https://github.com/influxdata/telegraf)
    - InfluxData on Log4j: [https://www.influxdata.com/blog/apache-log4j-vulnerability-cve-2021-44228/](https://www.influxdata.com/blog/apache-log4j-vulnerability-cve-2021-44228/)
  - **Source:** Legacy detection, no longer valid after transition to Telegraf

- **Plugin IDs:** `11852`, `10167`
  - **Descriptions:**
    - `11852`: MTA Open Mail Relaying Allowed (thorough test)
    - `10167`: NTMail3 Arbitrary Mail Relay
  - **Exception:** `SRS001`
  - **False Positives:** N/A
  - **Reason:** Both plugins test for the presence of mail relaying vulnerabilities. In our case, the SMTP server on `SRS001` is tightly controlled and configured to accept messages only from the internal sender address `noreply@atos.net`. Additionally, **NSX-T firewall rules** are in place to restrict SMTP access to trusted sources only, effectively mitigating any risk of unauthorized external relaying.

    These findings are considered **false positives**, as Nessus scans are executed from within the internal network—where mail relay restrictions do not apply the same way as they would to external senders.

    References:
    - Plugin 11852: [https://www.tenable.com/plugins/nessus/11852](https://www.tenable.com/plugins/nessus/11852)
    - Plugin 10167: [https://www.tenable.com/plugins/nessus/10167](https://www.tenable.com/plugins/nessus/10167)
    - Tenable community discussion: [False Positives and SMTP relay testing](https://tenable.my.site.com/s/question/0D5f200004rM1DUCA0/false-positive-on-smtp-plugin)
  - **Source:** VCS-606

- **Plugin IDs:** `155999`, `156057`, `156183`, `156327`
  - **Descriptions:**
    - `155999`: Apache Log4j < 2.15.0 Remote Code Execution (Nix)
    - `156057`: Apache Log4j 2.x < 2.16.0 Remote Code Execution (RCE)
    - `156183`: Apache Log4j 2.x < 2.17.0 Denial of Service (DoS)
    - `156327`: Apache Log4j 2.0 < 2.3.2 / 2.4 < 2.12.4 / 2.13 < 2.17.1 Remote Code Execution (RCE)
  - **Exception:** Removed
  - **False Positives:** N/A
  - **Reason:** These plugins report critical vulnerabilities in various versions of Apache Log4j 2.x, including the well-known Log4Shell (CVE-2021-44228) and other related CVEs (CVE-2021-45046, CVE-2021-45105, CVE-2021-44832). These issues were previously applicable to older versions of **Aria Operations for Automation (vRA)**.

    The environment has since been upgraded to **vRA 8.18**, which includes Log4j 2.17.1 or newer and is no longer affected by these vulnerabilities. The detections reported by Nessus are legacy artifacts or version signature-based false positives.

    References:
    - VMware KB 87120: [https://kb.vmware.com/s/article/87120](https://kb.vmware.com/s/article/87120)
    - VMware Security Advisory VMSA-2021-0028: [https://www.vmware.com/security/advisories/VMSA-2021-0028.html](https://www.vmware.com/security/advisories/VMSA-2021-0028.html)
    - Apache Log4j Security Overview: [https://logging.apache.org/log4j/2.x/security.html](https://logging.apache.org/log4j/2.x/security.html)
  - **Source:** Legacy detection, VCS-3822

- **Plugin ID:** `152534`
  - **Description:** VMware Workspace ONE Access / VMware Identity Manager Multiple Vulnerabilities (VMSA-2021-0016)
  - **Exception:** Removed
  - **False Positives:** N/A
  - **Reason:** This finding previously applied to versions of VMware Identity Manager (vIDM) that included vulnerabilities CVE-2021-22002 and CVE-2021-22003. As the environment has been upgraded to vIDM 3.3.7 or higher, which includes patches addressing these vulnerabilities, this finding is no longer applicable.
    References:
    - VMware Security Advisory VMSA-2021-0016: [https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/SecurityAdvisories/0/23608](https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/SecurityAdvisories/0/23608)
    - VMware KB 85254: [https://kb.omnissa.com/s/article/85254](https://kb.omnissa.com/s/article/85254)
  - **Source:** Legacy detection, VCS-4723

- **Plugin ID:** `138576`
  - **Description:** Oracle Java SE Multiple Vulnerabilities (Jul 2020 CPU)
  - **Exception:** `NES001`
  - **False Positives:** N/A
  - **Reason:** This plugin detects vulnerabilities addressed in Oracle's July 2020 Critical Patch Update (CPU), specifically targeting Java SE versions prior to 7u271, 8u261, 11.0.8, or 14.0.2.

    In our environment, the appropriate patches have been applied as per Oracle's advisory. However, Nessus continues to flag this vulnerability, which is a known issue due to the scanner's reliance on version string matching. This method can lead to false positives, especially when the version string does not reflect the applied patches accurately.

    References:
    - Tenable Plugin Details: [https://www.tenable.com/plugins/nessus/138576](https://www.tenable.com/plugins/nessus/138576)
    - Oracle July 2020 CPU Advisory: [https://www.oracle.com/security-alerts/cpujul2020.html](https://www.oracle.com/security-alerts/cpujul2020.html)
    - Tenable Community Discussion on False Positives: [https://community.tenable.com/s/question/0D5f200004rM08LCAS/java-70161-triggering-false-positives-on-multiple-plugins](https://community.tenable.com/s/question/0D5f200004rM08LCAS/java-70161-triggering-false-positives-on-multiple-plugins)
  - **Source:** Official documentation or technical justification not found

- **Plugin ID:** `156103`
  - **Description:** Apache Log4j 1.2 JMSAppender Remote Code Execution (CVE-2021-4104)
  - **Exception:** `NES001`
  - **False Positives:** N/A
  - **Reason:** This plugin detects the presence of Apache Log4j version 1.2, which is vulnerable to CVE-2021-4104 when specifically configured to use the `JMSAppender`. The vulnerability allows for remote code execution if an attacker can modify the Log4j configuration to include a malicious `JMSAppender` setup.

    In our environment, we have upgraded to Log4j version 2.16.0 or higher, which addresses this vulnerability. Additionally, our configurations do not utilize the `JMSAppender`, and we have audited our logging configurations to ensure its absence.

    Despite these measures, Nessus may continue to flag this vulnerability due to residual metadata or version strings that do not reflect the updated state.

    References:
    - Apache Log4j Security Vulnerabilities: [https://logging.apache.org/log4j/2.x/security.html](https://logging.apache.org/log4j/2.x/security.html)
    - Tenable Plugin Details: [https://www.tenable.com/plugins/nessus/156103](https://www.tenable.com/plugins/nessus/156103)
    - Red Hat CVE-2021-4104: [https://access.redhat.com/security/cve/CVE-2021-4104](https://access.redhat.com/security/cve/CVE-2021-4104)
  - **Source:** Official documentation or technical justification not found

- **Plugin IDs:** `161241`, `163304`, `174511`
  - **Descriptions:**
    - `161241`: Oracle Java SE Multiple Vulnerabilities (April 2022 CPU)
    - `163304`: Oracle Java SE Multiple Vulnerabilities (July 2022 CPU)
    - `174511`: Oracle Java SE Multiple Vulnerabilities (April 2023 CPU)
  - **Exception:** `NES001`
  - **False Positives:** N/A
  - **Reason:** These plugins detect vulnerabilities addressed in Oracle's Critical Patch Updates (CPUs) for Java SE in April 2022, July 2022, and April 2023. In our environment, the appropriate patches have been applied as per Oracle's advisories. However, Nessus continues to flag these vulnerabilities, which is a known issue due to the scanner's reliance on version string matching. This method can lead to false positives, especially when the version string does not reflect the applied patches accurately.

    References:
    - Oracle CPU April 2022: [https://www.oracle.com/security-alerts/cpuapr2022.html](https://www.oracle.com/security-alerts/cpuapr2022.html)
    - Oracle CPU July 2022: [https://www.oracle.com/security-alerts/cpujul2022.html](https://www.oracle.com/security-alerts/cpujul2022.html)
    - Oracle CPU April 2023: [https://www.oracle.com/security-alerts/cpuapr2023.html](https://www.oracle.com/security-alerts/cpuapr2023.html)
    - Tenable Plugin 161241: [https://www.tenable.com/plugins/nessus/161241](https://www.tenable.com/plugins/nessus/161241)
    - Tenable Plugin 163304: [https://www.tenable.com/plugins/nessus/163304](https://www.tenable.com/plugins/nessus/163304)
    - Tenable Plugin 174511: [https://www.tenable.com/plugins/nessus/174511](https://www.tenable.com/plugins/nessus/174511)
  - **Source:** Official documentation or technical justification not found

- **Plugin ID:** `171080`
  - **Description:** OpenSSL 1.0.2 < 1.0.2zg Multiple Vulnerabilities (July 2023 CPU)
  - **Exception:** Removed
  - **False Positives:** N/A
  - **Reason:** This finding previously applied to versions of VMware Aria Operations for Logs that included vulnerable versions of OpenSSL. As the environment has been upgraded to version 8.14 or higher, which includes updated OpenSSL libraries addressing these vulnerabilities, this finding is no longer applicable.

    References:
    - Tenable Plugin 171080: [https://www.tenable.com/plugins/nessus/171080](https://www.tenable.com/plugins/nessus/171080)
    - OpenSSL Vulnerabilities: [https://www.openssl.org/news/vulnerabilities-1.0.2.html](https://www.openssl.org/news/vulnerabilities-1.0.2.html)
  - **Source:** Official documentation or technical justification not found

- **Plugin ID:** `201194`
  - **Description:** OpenSSH < 9.8 RCE (CVE-2024-39894 / CVE-2024-6387)
  - **Exception:**
    - `VLI`, `vRA`, `OPS`, `SDM`
    - `VCS001`, `VCS002`
    - `SRM`
  - **False Positives:** N/A
  - **Reason:**
    - CVE-2024-39894 affects OpenSSH versions **9.5 through 9.7**. All systems listed — including vCenter Server Appliances (VCS001/VCS002), vRA, VLI, SDM, OPS — are running OpenSSH versions **lower than 9.5** (e.g., 7.8p1 in vCenter 7.0 U3t).
    - CVE-2024-6387 and related findings (e.g., CVE-2023-51384, CVE-2023-51385) are flagged in SRM 9.0.2 due to **rpm version scanning** and are confirmed **false positives**.
    - **Conclusion:** This exception does **not impact VCS** environments as **none of the affected components run OpenSSH versions between 9.5 and 9.8**. Therefore, **no remediation is required**.

    References:
    - [CVE-2024-39894 – NVD](https://nvd.nist.gov/vuln/detail/CVE-2024-39894)
    - [Broadcom KB 377358 – vCenter not affected](https://knowledge.broadcom.com/external/article/377358)
    - [Broadcom KB 383471 – SRM false positive](https://knowledge.broadcom.com/external/article/383471)
  - **Source:** VCS-14471, Broadcom KB

- **Plugin ID:** `201194`
  - **Vulnerability:** OpenSSH < 9.8 - Remote Code Execution (RCE)  
  - **CVE ID:**
    - [CVE-2024-6387](https://www.tenable.com/cve/CVE-2024-6387)
  - **Summary**: This vulnerability refers to a signal handler race condition in OpenSSH, potentially leading to remote code execution under certain conditions. Nessus has flagged this CVE on several Broadcom-based management platforms.
  - **Exceptions**:The following components are not impacted due to updated or patched OpenSSH versions:

    - **vROps (Aria Operations):**
      - Fixed in version `8.18.1`
      - OpenSSH version `9.3p2-10.ph5` is in use, which is beyond the affected range
      - Refernce document : [Aria Suite 8.x CVE-2024-39894](https://knowledge.broadcom.com/external/article?articleNumber=384533)
    - **CPX (Cloud Proxy):**
      - Uses the same OpenSSH version as vROps (`9.3p2-10.ph5`) and is not vulnerable
    - **SRM (Site Recovery Manager):**
      - Fixed in Photon OS 4 with OpenSSH version `8.9p1-8.ph4`
      - Version `9.0.2` includes the fix
      - Refernce document : [OpenSSH Vulnerabilities on Site Recovery manager and vSphere Replication](https://knowledge.broadcom.com/external/article?articleNumber=383471)
    - **VCS (vCenter Server):**
      - vCenter 7.x: Not impacted
      - vCenter 8.0 U3b: Uses OpenSSH `8.9p1-8`, which is not vulnerable
      - Refernce document : [CVE-2024-6387 - OpenSSH vulnerability](https://knowledge.broadcom.com/external/article?articleNumber=371126)
    - **vRLI (vRealize Log Insight):**
      - Apply **Hotfix 1** on version `8.18.0` to address the issue
      - Refernce document : [VMware Aria Operations for Logs 8.18 Hot Fix 1](https://knowledge.broadcom.com/external/article?articleNumber=373991)
    - **vRA (Aria Automation):**
      - Mitigation is done via playbook `upgradeAriaOpenSsh.yml` during update phase
      - Refernce document : [VMware Aria Automation for potential impact from CVE-2024-6387](https://knowledge.broadcom.com/external/article?articleNumber=372561)
    - **VSR (vSphere Replication):**
      - Same fix as SRM — addressed in Photon OS 4 version `8.9p1-8.ph4`
  - **False Positives:** N/A
  - **Notes:**
    - Nessus may continue to flag this CVE on Broadcom management platforms even if the above versions are in place. If verified, such detections can be treated as **false positives**.
  - **Source:** VCS-16335, Broadcom KB articles
