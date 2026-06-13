# Add/Modify/Remove IPAM entry in Infoblox

## Table of content

- [Add/Modify/Remove IPAM entry in Infoblox](#addmodifyremove-ipam-entry-in-infoblox)
  - [Table of content](#table-of-content)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
- [Actions possible to perform in Infoblox](#actions-possible-to-perform-in-infoblox)
  - [Starting point for operations](#starting-point-for-operations)
  - [Create new host record manually](#create-new-host-record-manually)
  - [Adding IPAM entries manually](#adding-ipam-entries-manually)
  - [Adding IPAM entries for non-existing hosts via Import function](#adding-ipam-entries-for-non-existing-hosts-via-import-function)
  - [Adding IPAM entries for existing host via import function](#adding-ipam-entries-for-existing-host-via-import-function)
  - [Adding host entries with multiple IP addresses via import function](#adding-host-entries-with-multiple-ip-addresses-via-import-function)
  - [Remove existing host record manually](#remove-existing-host-record-manually)
  - [Removing IPAM entries manually](#removing-ipam-entries-manually)
  - [Removing IPAM entries via Import function](#removing-ipam-entries-via-import-function)
  - [Create reserved IP range in IPAM](#create-reserved-ip-range-in-ipam)
  - [Delete reserved IP range in IPAM](#delete-reserved-ip-range-in-ipam)
  - [Duration](#duration)
  - [Risk](#risk)

## Changelog

| version | Date       |Issue| Description                 | Author(s)           |
| ------- | ---------- | ----| --------------------------- | ------------------- |
| 0.1     | 28.04.2022 |DHC-4642 | Initial version            | Michał Sobieraj |
| 0.2     | 09.05.2022 |DHC-4642 | Added instructions for adding/removing entries manually            | Michał Sobieraj |
| 0.3     | 11.05.2022 |DHC-4642 | Added additional instructions and reedited document          | Michał Sobieraj |

## Introduction

### Purpose

Add, modify or remove IPAM entries in Infoblox.

### Audience

- VCS Operations
- VCS Engineers

### Scope

Every action described in instruction can be executed independently. Every section below describes a separate operation that you can carry out in infoblox. Start with "Starting point for operations" and after that, move to the section which title describes your task the closest.

>Note: It's advised to read whole instruction to get familiar with options available in infoblox.

# Actions possible to perform in Infoblox

## Starting point for operations

1. Log into infoblox using terminal server of environment.
2. Go to "Data Management" tab on top and then "DNS" tab below it.
3. Make sure correct view in dropdown on top left (under infoblox logo) is set - "default" view contains entries for VCS components, when adding entries for customer VM's use view dedicated for that, it should have customer code in it's name.

>Note: Keep in mind that for big amount of entries it is recommended to use import option if possible.

## Create new host record manually

1. Under "Zones" tab in "DNS" choose domain that you want to create host in.
2. When in domain, in toolbar on the right choose "Add" -> "Host" -> "Host".
3. Set a name, choose IP addresses and adequate comment if needed.
4. Be sure that host you are creating don't require to change any other options available, and if so change them. Click "Next".
5. Add any extensible attributes if needed and click "Next".
6. Choose if you want to create Host now or schedule creation for later. After choosing your options click "Save & Close".
7. Repeat steps above for every host that is needed.

## Adding IPAM entries manually

1. To add entries manually you have to first locate the entry in domain and then click three lines next to checkbox on the left of entry. In menu that appears select "edit".
2. In Window that appears add additional IP reservations by adding entries to IPv4 table that is shown. After that click "Save & Close" to apply changes.
3. Repeat steps above for every host that is needed.

## Adding IPAM entries for non-existing hosts via Import function

1. In "DNS" tab under "Zones" go to "Records" new entries will show.
2. Go to "IPAM" tab and check if networks for IP's that will be added exist - if they don't they will have to be added using "Add" option in "Toolbar" on the right. If there are networks missing, ask requestor to provide details.
3. After completing these pre-checks create import files for entries that can be added automatically. Best practice is to import one file per domain, it also can't contain hostnames already existing in IPAM. You can use "[**IPAM import into infoblox.csv**](https://atos365.sharepoint.com/:x:/r/sites/DHCDevSecOpsTeam/Shared%20Documents/General/WorkInstructions/IPAM%20import%20templates/IPAM%20Import%20into%20Infoblox.csv?d=w3ba4b9e69c0e41cf80cd53b63625f701&csf=1&web=1&e=zxFjGh)" file as reference.

    >Note: Csv file can be downloaded. Each entry must have "hostrecord" and "hostaddress" line in import file, also make sure "network_view" and "view" are set to fit customer import is done for.

4. When the import files are prepared go back to Data Management -> DNS menu in infoblox and select domain, you will import into. Then select "CSV import" in Toolbar on the right.
5. In window that appears select "Add" option and click "Next".
6. Select file to import, leave "Stop import" option selected and then click "Import".
7. After import is completed you can repeat steps above for other domains.

## Adding IPAM entries for existing host via import function

In case where you need to add IPAM entries to existing hosts use "[**IPAM import into infoblox - Additional IP addresses.csv**](https://atos365.sharepoint.com/:x:/r/sites/DHCDevSecOpsTeam/Shared%20Documents/General/WorkInstructions/IPAM%20import%20templates/IPAM%20import%20into%20infoblox%20-%20Additional%20IP%20Addresses.csv?d=w9514ea2906d7401bbd2682b7e0f6d36b&csf=1&web=1&e=g5dBQ0)".

1. Fill the template and proceed as you'd do with adding new IPAM entries.

## Adding host entries with multiple IP addresses via import function

If you need to add hosts with more then one IP address in domain use "[**IPAM import into infoblox - Host with multiple IP addresses**](https://atos365.sharepoint.com/:x:/r/sites/DHCDevSecOpsTeam/Shared%20Documents/General/WorkInstructions/IPAM%20import%20templates/IPAM%20Import%20into%20Infoblox%20-%20adding%20host%20with%20multiple%20IP%20addresses.csv?d=w771a2202ac63424e8a1f7a9eaf6f1563&csf=1&web=1&e=b8FTGG) template.

1. Fill the template and proceed as you'd do with adding new IPAM entries.

## Remove existing host record manually

1. To remove existing host record go to "Zones" tab in "DNS" choose domain that host is inside of.
2. With the help of search bar on the right find host by "Name" or "IP".
3. Click the checkbox next to host name and go to the toolbar on the right and choose "delete" or "schedule delete". Remember that you can check many hosts to delete a few with one click.
4. If you chosen "schedule delete" set the time that you want your host to be deleted.
5. Repeat steps above for every host that is needed.

## Removing IPAM entries manually

1. To remove entries manually you have to locate the entry in domain and then click three lines next to checkbox on the left of entry. In menu that appears select "edit".
2. In Window that appears remove selected IP reservations by checking the right entries in IPv4 table that is shown and clicking delete. After that click "Save & Close" to apply changes.
3. Repeat steps above for every host that is needed.

## Removing IPAM entries via Import function

1. Go to "IPAM" tab and check for networks with IP's that will be removed exist.
2. After completing these pre-checks create import files for entries that can be removed automatically. There should be one import file per domain. You can use same import file as for Adding IPAM entries as reference.
3. When the import files are prepared go back to "DNS" tab in infoblox and select domain from the "Zones" tab, you will import into. Then select "CSV import" in Toolbar on the right.
4. In window that appears select "Delete" option and click "Next".
5. Select file to import, leave "Stop import" option selected and then click "Import".
6. After import is completed you can repeat steps above for other domains.

>Note: Deleting IPAM entries via import function requires only hostrecord entries. If you use file with hostaddress entries the infoblox will stop importing right at first hostaddress row (probably somewhere in the middle of your file) with error message despite successful output of the operation. In such situation you can proceed further as entries from prepared files will be deleted.

## Create reserved IP range in IPAM

1. Under "Data Management" tab, choose the "IPAM" tab, in the toolbar on the right choose "Range" and "IPv4" in "Add" option.
2. Check "Add Range" and click next.
3. Choose Network, Ip range from start to end, name and comment accordingly. Click next.
4. Check "None (Reserved Range)" and click next.
5. Move through next page by clicking next.
6. Set Date and time accordingly to when the IP reservation is planned. Click "Save & Close".
7. Repeat steps above as many times as needed.

## Delete reserved IP range in IPAM

1. To Delete reserved IP range you should choose "DHCP" tab under "Data Management".
2. In "Networks" tab, find network that your IP range is set in.
3. Find the IP address Range entry, check it and on the right side in toolbar click "Delete".

## Duration

The import/removal/modification of prepared files is a quick process if the preparations and pre-checks were done accordingly. The real time-consuming task is preparation of files to be imported which heavily depends on number of
entries to be added to the IPAM. It might be anywhere from 10 minutes (for new IP's) to 2 hours (for big IP's databases containing hundreds of entries).

## Risk

Adding/Removing/Modifying IP reservations if done correctly is a low risk task with virtually no negative impact.
