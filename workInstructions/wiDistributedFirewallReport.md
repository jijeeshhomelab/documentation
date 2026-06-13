# Distributed Firewall Report

## Changelog

 |    Date    |  TOS   | Author | Description |
 |---|---|---|---|
 | 28.01.2025 | DHC 1.9 | Cezary Dwojak | Initial draft creation |

## Introduction

Distributed Firewall reports provides Firewall Ruleset gathered from Firewall in JSON format allowing to easily be imported to i.e. excel file.

### Purpose

Create report of current Distributed Firewall Ruleset

### Audience

Engineers, architect, user who needs to gather the firewall ruleset

## Scope

Run ansible playbook to gather Distributed Firewall Ruleset in JSON format.

### Requirements

Receipents list is configured to "<DHC-DevSecOps@atos.net>" in default folder and this is a place once this needs to be changed.

Certificate automation05 is used to retrieve passwords from HSV. The playbook will not work unless it is present in the environment and on Ansible VM in `/root/.ssl` (or else as specificed by the role defaults). This can be modified in distributedFirewallReport.yml.

### Dependecies

1. dhc-readSecretVaultEntry
2. dhc-connectionWrapper

### Role Variables

Role variables are defined in /roles/dhc-distributedFirewallReport/defaults/main.yml

|  Variable  |     Description/Values    |   Source   |
|------------|---------------------------|------------|
| nsxUser | NSX user used to login to NSX Manager | role defaults |
| scriptDirectory | Directory where all script files will be kept for task duration - default is : C:\\Users\\svc-{{ locationCode }}-ans01 | role defaults |
| email | E-mail address used to send all the mails to | role defaults |
| certificate | Name of certificate used to encrypt Rules and Structure files <BR> if certiface already exist on TSS01 it is reused, otherwise it is crated and sent via e-mail | role defaults |

### Powershell Scripts used

In `./roles/dhc-DistributedFirewallReport/files` 3 files are located. Those files are:  

| Files name | Purpose | is sent over mail? |
|---|----|---|
| psDfwFirewallReport.ps1 | This is main script performing all actions | no |
| psImportCertificate.ps1 | This is script for importing certificate on destination machine.<br>It helps end user to import certificate which is required to decode encrypted files | yes - as encrypted 7z archive |
| psDecryptFile.ps1 | This script is for decryting report files.<br> It helps end user to decrypt encrypted files | yes - as encrypted 7z archive |

### Powershell Script details

Powershell variables are defined in Run PowerShell script psDfwFirewallReport.ps1 task in distributedFirewallReport.yml.

| Parameter  | Description | Values | Default | Definition |
|---|---|---|---|---|
| NSX_MANAGER | Defines NSX manager report will be created on | inherited from {{  mgmtDns.nsx002.name }}.{{ searchDomain }} | {{  mgmtDns.nsx002.name }}.{{ searchDomain }} | hardcoded - not to be changed |
| CERT_NAME | Defines the name of certificate to be used to encrypt Rules and Structure data | inherited from {{ certificate }} | {{ certificate }} | changeable in task variables |
| MAIL | Defines destinnation e-mail address where report will be sent | inherited from {{ email }} | {{ email }} | changeable in task variables |
| SEND_CERTIFICATE | Defines if certificate should be sent or not (during certificate creation is set to "on" by default) | needs to be defined in DistributedFirewallReport in powershell script execution line | not used | Switch parameter - does not need any value to be provided|
| NSX_USERNAME | Defines NSX username used to login to NSX Manager | inherited from {{ nsxUser }} | {{ nsxUser }} | hardcoded - not to be changed |
| NSX_PASSWORD | Defines NSX password used to login to NSX Manager | inherited from {{ nsxPassword }} | {{ nsxnsxPassword }} | hardcoded - not to be changed |
| TENANT | Defines which tenant should the report be done for | needs to be defined in DistributedFirewallReport in powershell script execution line  | not defined - treated as all tenants | If no tenant is defined entire Distributed Firewall is being performed |

In case dedicated tenant is supposed to get collected switch -TENANT needs to be added with following tenant name.
For NSX 3.x version, tenant name specifies the name of policy in Distributed Firewall Ruleset and it is defined like a part of the name i.e. if Policy is named ATOS-ALL-IN and you will add -TENANT "ATOS" : policy will be recognized and script will collect all firewal rules from this policy

For NSX 4.x TENANT parameter defines exact project name as an argument.

### Running the script

To run the script file distributedFirewallReport.yml needs to be executed with ansible-playbook command (keep in mind specified {path} needs to be provided, in case you run file from the same directory {path} can be omitted):

```shell
ansible-plabyook {path}/distributedFirewallReport.yml
```

Last task may take some time (counted in many minutes) depending on complexity and size of the environment.  

### Decrypting firewall rules and structure files

Once playbook is finished, 1 or 4 mails are received:

- 4 in case certificate is delivered (usually only for the first time)
  
  - DHC-\<customer_abbreviation_name\>-\<location\>-DFW_REPORT

    - encrypted firewall rules and firewall ruleset structure are sent with this mail
    - archive password protected script to decrypt firewall rules and structure files
  
  - DHC-\<customer_abbreviation_name\>-\<location\>-DFW_REPORT ( Certificate )

    - password protected archive with certificate is with this mail
    - archive password protected script to import certificate to certificate manager

  - DHC-\<customer_abbreviation_name\>-\<location\>-DFW_REPORT ( Certificate - extract password )

    - password for certificate import is sent protected with this mail
  
  - DHC-\<customer_abbreviation_name\>-\<location\>-DFW_REPORT + password
  
    - password for archive to unzip certificate (7zip is required)

- 1 in case certificate is not sent as already exist and no additional request to send certificate was set

  - DHC-\<customer_abbreviation_name\>-\<location\>-DFW_REPORT

    - encrypted firewall rules and firewall ruleset structure are sent with this mail
    - archive password protected script to decrypt firewall rules and structure files
  
Save all the files in dedicated folder.

- To perform certificate import activities, please collect all required data like:

  - password to unzip 7zip archive with certificate
  - password to import certificate - being asked for this during powershell script running

- To perform decryption of firewall ruleset and structure files, please collect all required data like:
  
  - encrypted files of firewall ruleset and structure

#### Certificate import steps

1. Run Powershell script
    ![Picture 1. 1-1 Certificate Import](images/wiDistributedFirewallReport/1-1_Certificate_import_PS_start.png)

2. Select proper .pfx file

    ![Picture 2. 1-2 Certificate Import](images/wiDistributedFirewallReport/1-2_Certificate_import_select_certificate.png)

3. Once file is selected you need to provide password to import certificate. This is obtained from e-mail named DHC-\<customer_abbreviation_name\>-\<location\>-DFW_REPORT ( Certificate - extract password )

    ![Picture 3. 1-3 Certificate Import](images/wiDistributedFirewallReport/1-3_Certificate_import_provide_password.png)

4. Confirmation certificate import is successfull

    ![Picture 4. 1-4 Certificate Import](images/wiDistributedFirewallReport/1-4_Certificate_import_success_confirmation.png)

5. Confirmation certificate import failed. Cannot proceed until this import is successfull. Check password for import.

    ![Picture 5. 1-5 Certificate Import](images/wiDistributedFirewallReport/1-5_Certificate_import_failure_confirmation.png)

#### Decrypt encrypted firewall ruleset and structure files

1. Run Powershell script

    ![Picture 6. 2-1 Decrypt File](images/wiDistributedFirewallReport/2-1_Decrypt_file_ps_start.png)

2. Select proper .enc file

    ![Picture 7. 2-2 Decrypt File](images/wiDistributedFirewallReport/2-2_Decrypt_file_select_enc_files.png)

3. Confirmation decryption is successfull

    ![Picture 8. 2-3 Decrypt File](images/wiDistributedFirewallReport/2-3_Decrypt_file_select_decrypt_success.png)

4. Confirmation decryption failed. Check, if necessary certificates are installed/imported.

    ![Picture 9. 2-4 Decrypt File](images/wiDistributedFirewallReport/2-4_Decrypt_file_select_decrypt_failure.png)

#### Import encrypted firewall ruleset to excel

1. Open excel file and move to "Data" tab and select "Get Data", next "From File" and finally "From JSON"

    ![Picture 10. 3-1 JSON to Excel](images/wiDistributedFirewallReport/3-1_Json_to_excel_select_import.png)

2. Select file which was initially decrypted with firewall ruleset in json format

    ![Picture 11. 3-2 JSON to Excel](images/wiDistributedFirewallReport/3-2_Json_to_excel_select_file.png)

3. Once file is imported on new window "To Table" needs to be clicked

    ![Picture 12. 3-3 JSON to Excel](images/wiDistributedFirewallReport/3-3_Json_to_excel_convert_to_table.png)

4. New window pops up, just confirm "OK" with no configuration changes

    ![Picture 13. 3-4 JSON to Excel](images/wiDistributedFirewallReport/3-4_Json_to_excel_convert_to_table_confirm.png)

5. Once converted to table, there is button with 2 arrows pointing to different directions, click this

    ![Picture 14. 3-5 JSON to Excel](images/wiDistributedFirewallReport/3-5_Json_to_excel_extend_column.png)

6. "Load more" needs to be clicked to ensure all columns are marked to be extended

    ![Picture 15. 3-6 JSON to Excel](images/wiDistributedFirewallReport/3-6_Json_to_excel_extend_column_load_more.png)

7. Once all columns are loaded click "OK"

    ![Picture 16. 3-7 JSON to Excel](images/wiDistributedFirewallReport/3-7_Json_to_excel_extend_column_load_more_after_and_ok.png)

8. Some of columns will need to extract values, there is same icon with 2 arrows, each column need to be worked individually.

    ![Picture 17. 3-8 JSON to Excel](images/wiDistributedFirewallReport/3-8_Json_to_excel_extend_data_column.png)

9. Click on 2 arrow icon and select "Extract Values..."

    ![Picture 18. 3-9 JSON to Excel](images/wiDistributedFirewallReport/3-9_Json_to_excel_extend_data_column_extract_values.png)

10. New window pops us and needs to be set up as on picture:
    1. Delimeter to be selected as "--Custom--"
    2. Concatenation must be marked for special characters
    3. "Carriage Return and Line Feed" must be selected from special character list
    4. Once all is set up click "OK" button
    5. This activitity must be performed for all the columns with 2 arrow icon

    ![Picture 19. 3-10 JSON to Excel](images/wiDistributedFirewallReport/3-10_Json_to_excel_extend_data_column_extract_values_configuration.png)

11. Once all the columns are reworked and ready click "Close & Load" button

    ![Picture 20. 3-11 JSON to Excel](images/wiDistributedFirewallReport/3-11_Json_to_excel_close_and_load.png)

12. Excel file will be loaded, mark all the cells to be selected

    ![Picture 21. 3-12 JSON to Excel](images/wiDistributedFirewallReport/3-12_Json_to_excel_ready_for_wrap.png)

13. On "Home" tab click "Wrap Text" icon to properly format the file.

    ![Picture 22. 3-13 JSON to Excel](images/wiDistributedFirewallReport/3-13_Json_to_excel_wrap_text.png)

14. File is ready to be delivered to requester
