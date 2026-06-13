# VRA Cloud and IPAM Integration Tests

## Introduction

### Purpose

Perform final validation tests after Infoblox integration with VRA Cloud.

### Audience

- VCS Operations

### Scope

Validate functionality of Infoblox integration with VRA Cloud includes:

- Verify IP assignment
- Verify IP releasing
- Verify IP Pool control
- Verify IP assignment while Infoblox is not operational
- Verify IP assignment while Pool is full
- Verify IP assignment when simultaneous deployments are done

Elements excluded from scope:

- VRA Cloud functionality
- Infoblox DNS availability

## Used abbreviations

| Abbreviation | Description                                 |
|--------------|---------------------------------------------|
| VCS          | VMware Cloud Services                       |
| DNS          | Domain Name Server                          |
| VM           | Virtual Machine                             |
| IP           | Internet Protocol                           |
| VRA          | vRealize Automation                         |
| VLAN         | Virtual Local Area Network                  |

## Target audience

- RS&D VCS Architects
- RS&D VCS Build Engineers
- RS&D VCS Deployment Managers
- Cloud Tower Service Managers

# Overview of steps involved

## Test method

Team members from the test team will act as the customer, will be instructed to
behave as such. This means they will not use their technical knowledge to
resolve issues or alter the system/application configuration if things do not
work as expected.

Issues will be logged in a copy of the aforementioned Test Plan excel
spreadsheet which will be dedicated to a specific build project.

Issues shall be passed on to the build team for further investigation and
resolution.

Once a specific issue is fixed, a retest will take place.

Tests shall be executed in parallel, if possible from technical and manpower
point of view. product.

# Test Cases

<style>
table {
    width:100%;
}
</style>

<table>
    <thead>
        <tr>
            <th>Test</th>
            <th>Test Aspect</th>
            <th>Expected Results</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3>1</td>
            <td>Functionality</td>
            <td>Deploy VM in particular network profile which is using IP Pool from Infoblox (integration with infoblox should be in place to achieve this).</td>
        </tr>
        <tr>
            <td>Procedure</td>
            <td>
               Step 1) Add IP Pool in Infoblox <br>
               Step 2) Add Address Space in Infoblox (VLAN is possible name) <br>
               Step 3) Create VRA Cloud Network Profile using previously created IP pool in Infoblox (add tag to identify later by VM) <br>
               Step 4) Create VM (windows and Linux) and assign it to Network Profile <br>
               Step 5) Validate creation of the VM and assigned IP address <br>
               Step 6) Validate address reservation in Infoblox in particular IP Pool
            </td>
        </tr>
        <tr>
            <td>Expected Results</td>
            <td>Creation of the VM should end with success, IP should be added to VM, Information about VM's IP and it's name should be reported in Infoblox database</td>
        </tr>
    </tbody>
</table>
<br><br><br>

<table>
    <thead>
        <tr>
            <th>Test</th>
            <th>Test Aspect</th>
            <th>Expected Results</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3>2</td>
            <td>Functionality</td>
            <td>Remove VM from particular Network profile (this from above)Remove VM from VRA Cloud</td>
        </tr>
        <tr>
            <td>Procedure</td>
            <td>
               Step 1) Remove Deployment from VRA Cloud <br>
               Step 2) Validate removal in VRA Cloud <br>
               Step 3) Validate removal from Infoblox and releasing IP address
            </td>
        </tr>
        <tr>
            <td>Expected Results</td>
            <td>Removal of the VM should be successful from VRA Cloud and from Vcenter. Removal of the reference to this VM in Infoblox should be removed and IP should be released.</td>
        </tr>
    </tbody>
</table>
<br><br><br>

<table>
    <thead>
        <tr>
            <th>Test</th>
            <th>Test Aspect</th>
            <th>Expected Results</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3>3</td>
            <td>Functionality</td>
            <td>Create small IP Pool in Infoblox and validate different circumstances</td>
        </tr>
        <tr>
            <td>Procedure</td>
            <td>
               Step 1) Add IP Pool in Infoblox (/30) <br>
               Step 2) Add address space in infoblox <br>
               Step 3) Create VRA Cloud Network Profile using previously create IP Pool <br>
               Step 4) Create that many VMs using this profile to fill all available addresses<br>
               Step 5) Create new VM and validate if it will fail due to lack of IP addresses<br>
               Step 6) If yes, remove one VM and validate release of IP in IP Pool <br>
               Step 7) Create one VM to validate if released IP is usable
            </td>
        </tr>
        <tr>
            <td>Expected Results</td>
            <td>Creation of the IP Pool in Infoblox should be successful, VM deployment to already utilized network should fail, once Pool will be released, new deployments should end up with success.</td>
        </tr>
    </tbody>
</table>
<br><br><br>

<table>
    <thead>
        <tr>
            <th>Test</th>
            <th>Test Aspect</th>
            <th>Expected Results</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3>4</td>
            <td>Functionality</td>
            <td>Create static and dynamic assignment of IP in VRA Cloud. Verify in Infoblox will register both types.</td>
        </tr>
        <tr>
            <td>Procedure</td>
            <td>
               Step 1) Add IP Pool in Infoblox <br>
               Step 2) Add Address Space in Infoblox (VLAN is possible name) <br>
               Step 3) Create VRA Cloud Network Profile using previously created IP pool in Infoblox (add tag to identify later by VM) <br>
               Step 4) Create VM (windows or Linux) and assign it to Network Profile (static address) - during deployment define IP by typing IP address <br>
               Step 5) Create VM (windows or Linux) and assign it to Network Profile (dynamic address based on tag) - during deployment define assign pool <br>
               Step 6) Validate if dynamic discovery of the static assignment is available and duplicate is avoided
            </td>
        </tr>
        <tr>
            <td>Expected Results</td>
            <td>Both VMs should be properly deployed and their IPs should be reflected in Infoblox</td>
        </tr>
    </tbody>
</table>
<br><br><br>

<table>
    <thead>
        <tr>
            <th>Test</th>
            <th>Test Aspect</th>
            <th>Expected Results</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3>5</td>
            <td>Functionality</td>
            <td>Check if already deployed VM's configuration can be changed and those changes would be reflected in Infoblox.</td>
        </tr>
        <tr>
            <td>Procedure</td>
            <td>
               Step 1) Add IP Pool in Infoblox <br>
               Step 2) Add Address Space in Infoblox (VLAN is possible name) <br>
               Step 3) Create VRA Cloud Network Profile using previously created IP pool in Infoblox (add tag to identify later by VM) <br>
               Step 4) Create VM (windows or Linux) and assign it to Network Profile (dynamic address) <br>
               Step 5) Validate if Infoblox database have been updated <br>
               Step 6) Change previously created VM's IP address to static (different from this pool) <br>
               Step 7) Validate if Infoblox database would be updated
            </td>
        </tr>
        <tr>
            <td>Expected Results</td>
            <td>Change of the IP on VM should be reflected in VRA Cloud as well as Infoblox.</td>
        </tr>
    </tbody>
</table>
<br><br><br>

<table>
    <thead>
        <tr>
            <th>Test</th>
            <th>Test Aspect</th>
            <th>Expected Results</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3>6</td>
            <td>Functionality</td>
            <td>Check if remove of the IP Pool in Infoblox is possible if entries are present.</td>
        </tr>
        <tr>
            <td>Procedure</td>
            <td>
               Step 1) Add IP Pool in Infoblox <br>
               Step 2) Add Address Space in Infoblox (VLAN is possible name) <br>
               Step 3) Create VRA Cloud Network Profile using previously created IP pool in Infoblox (add tag to identify later by VM)  <br>
               Step 4) Create VM (windows or Linux) and assign it to Network Profile (dynamic address) <br>
               Step 5) Validate if Infoblox database have been updated <br>
               Step 6) Remove Infoblox IP Pool <br>
               Step 7) Validate what implication it will cause in VRA Cloud
            </td>
        </tr>
        <tr>
            <td>Expected Results</td>
            <td>Removal IP Pool from Infoblox should generate popup which is alerting that removal of such IP Pool will cause data loss.</td>
        </tr>
    </tbody>
</table>

<br><br><br>

<table>
    <thead>
        <tr>
            <th>Test</th>
            <th>Test Aspect</th>
            <th>Expected Results</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3>7</td>
            <td>Functionality</td>
            <td>Turn off Infoblox and validate if creation of the VM to Infoblox IP Pool (Network Profile) can be performed.</td>
        </tr>
        <tr>
            <td>Procedure</td>
            <td>
               Step 1) Add IP Pool in Infoblox <br>
               Step 2) Add Address Space in Infoblox (VLAN is possible name) <br>
               Step 3) Create VRA Cloud Network Profile using previously created IP pool in Infoblox (add tag to identify later by VM) <br>
               Step 4) Turn Off Infoblox <br>
               Step 5) Create VM (windows or Linux) and assign it to Network Profile (dynamic address) <br>
               Step 6) Turn on Infoblox <br>
               Step 7) Validate if Infoblox database have been updated
            </td>
        </tr>
        <tr>
            <td>Expected Results</td>
            <td>Once Infoblox is not reachable, VRA Cloud should inform via alert that deployment will not be possible due to Infoblox unavailability.</td>
        </tr>
    </tbody>
</table>

<br><br><br>

<table>
    <thead>
        <tr>
            <th>Test</th>
            <th>Test Aspect</th>
            <th>Expected Results</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3>8</td>
            <td>Functionality</td>
            <td>Create multiple VMs simultaneously on a same pool and validate Infoblox reaction.</td>
        </tr>
        <tr>
            <td>Procedure</td>
            <td>
               Step 1) Add IP Pool in Infoblox <br>
               Step 2) Add Address Space in Infoblox (VLAN is possible name) <br>
               Step 3) Create VRA Cloud Network Profile using previously created IP pool in Infoblox (add tag to identify later by VM) <br>
               Step 4) Create 5 VMs (windows or Linux) and assign them to same Network Profile (dynamic address) <br>
               Step 5) Validate if Infoblox database have been updated
            </td>
        </tr>
        <tr>
            <td>Expected Results</td>
            <td>No matter how many VMs will be deployed at the same time, Infoblox is sending information about IP address availability so no duplicates should be found.</td>
        </tr>
    </tbody>
</table>
