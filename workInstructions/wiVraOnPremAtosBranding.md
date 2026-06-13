# vRA On-prem Atos Branding

# Table of Contents

- [vRA On-prem Atos Branding](#vra-on-prem-atos-branding)
- [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
- [vRA On-prem Branding](#vra-on-prem-branding)
  - [vRA Service Broker Branding](#vra-service-broker-branding)
    - [Branding Validation](#branding-validation)
  - [IDM User Branding](#idm-user-branding)
  - [IDM System Branding](#idm-system-branding)

# Changelog

| Date       | TOS     | Issue       | Author         | Description                          |
|------------|---------|-------------|----------------|--------------------------------------|
| 08.12.2022 | VCS 1.7 | CESDHC-5080 | Maikal Kumar   | First version                        |
| 15.12.2022 | VCS 1.7 | CESDHC-5080 | Vishnu Panchal | Screenshot, emblem and other updates |

## Introduction

### Purpose

Update branding for vRA on-prem.

### Audience

- VCS Operations

### Scope

- vRA service broker page branding
- IDM user branding
- IDM system branding

# vRA On-prem Branding

## vRA Service Broker Branding

For existing customer's service broker branding, follow below procedure.

- Login to ansible server.

![login_to_Ansible_Machine](images/customizeVra/images/logintoansibleserver.JPG)

- Execute ansible playbook by navigating to /opt/dhc/manage directory. "ansible-playbook customizeBranding.yml"

![customizeBranding_playbook_run](images/customizeVra/images/executeplaybook.JPG)

![customizeBranding_playbook_task](images/customizeVra/images/customizeBranding_playbook_task.JPG)

In case of new VCS deployments and vRA on-prem onboarding, branding is added as task in ***configureVraOnPremTenant.yml*** playbook which will be executed during tenant configuration. Service broker branding will be applied automatically after successful execution of tenant configuration playbook.

Note- IDM user and system branding is done with manual steps which should be followed for new or existing vRA on-prem branding customization.

### Branding Validation

After successful execution of customizeBranding.yml, branding for service broker will be applied and can be validated by logging-in to vRA.

![customized_screen](images/customizeVra/images/customized_screen.JPG)

![cloudAssemblyafterbranding](images/customizeVra/images/cloudAssemblyafterbranding.JPG)

![servicebrokerAfterbranding](images/customizeVra/images/servicebrokerAfterbranding.JPG)

## IDM User Branding

- Open vIDM login page & click on "Change to a different domain".

![login-screen](images/customizeVra/images/login-screen.JPG)

- Select "System Domain" from the drop down list.

![choose_system_domain](images/customizeVra/images/choose_system_domain.JPG)

- Login using "admin" user and password in system domain.

![change-system-domain-link](images/customizeVra/images/change-system-domain-link.JPG)

- Once logged-in, navigate to IDM Dashboard screen.

![Idm_dashboard_screen](images/customizeVra/images/Idm_dashboard_screen.JPG)

- Navigate to catalog tab, select "setting".

![catalog_setting](images/customizeVra/images/catalog_setting.JPG)

- Select "User Portal Branding" from the left pane.

![select_userportal_branding](images/customizeVra/images/select_userportal_branding.JPG)

Color code and specifications for "User Portal Branding".

| Properties                      | Value                                                                                                       |
|---------------------------------|-------------------------------------------------------------------------------------------------------------|
| Masthead Background Color       | #0066A1                                                                                                     |
| Masthead Text Color             | #FFFFFF                                                                                                     |
| Background Color                | #EBEEF1                                                                                                     |
| Icon Background Color           | #FFFFFF                                                                                                     |
| Icon Background Opacity         | 100%                                                                                                        |
| Name and Icon Color             | #414b57                                                                                                     |
| Lettering Effect                | None                                                                                                        |
| Welcome Screen Logo             | Download Atos emblem from github ***workInstructions/images/customizeVra/images/Atoslogoscaledemblem.png*** |
| Welcome Screen Background Color | #FFFFFF                                                                                                     |
| Welcome Screen Button Color     | #1DAEF2                                                                                                     |
| Welcome Screen Font Color       | #414B57                                                                                                     |

- Update all color codes in user portal  as shown in below screenshot.

![user_portal_screen_with_Preview](images/customizeVra/images/vpuserportalcust1.JPG)

- In the "Desktop Out-of-Box Experience" section, upload Atos emblem which will be displayed in the browser navigation.

![welcomescreenemblemupload](images/customizeVra/images/desktopemblemupload.JPG)

- Click Upload.

![chooseemblem](images/customizeVra/images/chooseemblem.JPG)

- Click open on selected emblem image.

![saveemblemandwelcomemsg](images/customizeVra/images/clicksaveonemblemuploadandwelcomemsg.JPG)

- Update Welcome Message as "Welcome to Atos VMware Cloud Services Identity Manager".
- Click "Save".

## IDM System Branding

IDM system branding will udpate logo, text, color, background color, company name, product name etc..

- Login using "admin" user in "system domain".

![change-system-domain-link](images/customizeVra/images/change-system-domain-link.JPG)

![choose_system_domain](images/customizeVra/images/choose_system_domain.JPG)

- Once loggedin to IDM, click on "Identity & Access Management", then click on "setup" button at right side.

![Identityandaccess_management](images/customizeVra/images/Identityandaccess_management.JPG)

- Select "Custom Branding".

![setup_and_custom_branding](images/customizeVra/images/setup_and_custom_branding.JPG)

- In the "Names & Logos" section, enter below details to change default vmware text:
Company Name "Atos"
Product Name "VMware Cloud Services"

![name_and_logo_screen](images/customizeVra/images/name_and_logo_screen.JPG)

- In Favicon, click on upload and select Atos emblem.

![chooseemblem](images/customizeVra/images/chooseemblem.JPG)

- Select and click Open to upload emblem file.
- A confirmation pop-up will appear to change emblem.

![emblem change confirmation](images/customizeVra/images/relaceemblemconfirmation.JPG)

- Click "Confirm".
- Updated favicon can be noticed in below screen.

![favicon final](images/customizeVra/images/userfaviconuploadedclicksave.JPG)

- Click "Save" to apply changes.
- Verify favicon changes in browser navigation as below.

![browser emblem verification](images/customizeVra/images/emblemverifyinbrowser.JPG)

- Now, navigate to "Sign-In Screen" tab in "Custom Branding" section.

Color codes and specifications for "sign-in-screen":

| Type                          | Color Code                                                                                                   |
|-------------------------------|--------------------------------------------------------------------------------------------------------------|
| logo                          | Download logo file from github documentation- workInstructions/images/customizeVra/images/Atoslogoscaled.png |
| Background color              | #ebeff1                                                                                                      |
| Box Background color          | #ffffff                                                                                                      |
| Login button background color | #00ADF5                                                                                                      |
| Login button text color       | #ffffff                                                                                                      |

![sign-in-screen-with-preview](images/customizeVra/images/sign-in-screen-with-preview.JPG)

- Click on upload to upload Atos logo.
![select atos logo](images/customizeVra/images/chooseatoslogo.JPG)

- Select Atos logo file and click open to upload it.
- After file is uploaded, preview will look like as in below screenshot.

![logo preview](images/customizeVra/images/clicksaveafterlogoupload.JPG)

- Update all color codes as mentioned in above table.
- Click "Save"
- Now, rebranded welcome screen of IDM should look as in below image.

![welcomescreen final](images/customizeVra/images/finalwelcomescreenwithnewlog.JPG)

Note- No changes should be done to color code, opacity, logo apart from the specifications given in this document.
