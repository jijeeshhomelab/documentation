# Exclude Abstraction Layer from VCS Monitoring

- [Exclude Abstraction Layer from VCS Monitoring](#exclude-abstraction-layer-from-vcs-monitoring)
- [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Related Documents](#related-documents)
- [Prerequisities](#prerequisities)
  - [Infrastructure access](#infrastructure-access)
  - [GitHub repository access](#github-repository-access)
  - [ServiceNow Account](#servicenow-account)
- [Automated VCS Monitoring Update](#automated-vcs-monitoring-update)
- [Postchecks](#postchecks)
  - [Validate the HTTP Gateway logs](#validate-the-http-gateway-logs)
  - [Validate ServiceNow Events](#validate-servicenow-events)
  - [Example of valid alert logs](#example-of-valid-alert-logs)
- [Rollback](#rollback)

# Changelog

| Date    | Issue      | Author   |  Description  |
| ------- | ---------- | -------- | --------------- |
| 27.07.2022 | AMC-3982 | Marcin Kujawski | Create work instruction document |

## Introduction

### Purpose

Modify VCS monitoring and exclude Abstraction Layer from a ticket creation process.

### Audience

- Customer Engagement
- VCS Operations

### Scope

Assumption is that the reader has reasonable grasp of VMware cloud technologies, virtualization, hyperconnected infrastructure, VCS, ServiceNow, and given customer environment specification as well as familiarity with architecture principles including single and multi-tenancy.  
This work instruction is intended to cover below components and domains:

1. Prerequisites to execute playbook
2. Detailed steps to execute automated VCS monitoring modification process
3. Postchecks

## Related Documents

This document is a subset of Atos Technology Lifecycle Management (ATLM) artefacts.

| Document                          | Document Name                                                                                                 |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------|
| VCS Infrastructure LLD |[lldInfrastructure](../design/lldInfrastructure.md)   |

# Prerequisities

Following chapter describes mandatory prerequisites steps that needs to be met before update VCS monitoring process starts.

## Infrastructure access

In order to execute playbooks access to Ansible core VM is requried. Please login to server with your personal account using domain authentication to validate if you have proper access.

## GitHub repository access

In order to execute playbooks github repository access is required. To retrieve automation code please go to DHC-Manage subdirectory and make sure role named **dhc-excludeAbsLayerFromMonitoring** and playbook **excludeAbsLayerFromMonitoring.yml** are there. If not - please refresh the repository by typing:

```shell
(ans210-std) dhc/manage$ git pull
```

## ServiceNow Account

To allow event creation in SNOW via SOAP request, HTTP Gateway server requires dedicated event account with proper rights.
Until now, Abstraction Layer should keep that account as it's used to create such events.
Please contact Abstraction Layer team and gather credentials for this event account used in SNOW.

Contacts are:

- Tulaskar, Vivekanand <Vivekanand.Tulaskar@atos.net>
- Bergen, Rene <Rene.Bergen@atos.net>
- Brondo, Gregory <Greg.Brondo@atos.net>

>**Note:** You will be prompted for a Username and Password during playbook execution. If you don't have such details please do not execute automation.

# Automated VCS Monitoring Update

To begin with automated process of updating the VCS monitoring on HTTP Gateway application, make sure to logon into Ansible core VM and navigate to the location where automation code was cloned (most likely user home directory).

Navigate to **manage** directory and execute following command to start automated VCS monitoring update:

```ansible
(ans210-std) dhc/manage$ ansible-playbook excludeAbsLayerFromMonitoring.yml 
```

Steps covered by automation:

- stop the application
- create a backup folder
- copy application files into it before doing any changes (folder location: `/opt/pubsubpy3.7/dpcop-pubsub-http-gw-master/pubsubhttpgateway/backup_files`)
- overwrite two python files: `cloudevent.py` and `backend.py`
- modify `entrypoint.sh` - remove spare lines and add credential details for SNOW event account
- restart the application
- make a copy of config.json file on vROPs nodes (folder location: `/usr/lib/vmware-vcops/user/plugins/outbound/vrops-generic-rest-plugin/conf/servicenow/`)
- overwrite config.json configuration file
- restart nodes (one by one)
- validate md5 checksum

**Note:** Playbook supports following tags to run selected tasks:

> - Executes mandatory task to update HTTP Gateway code `dhc/manage$ ansible-playbook excludeAbsLayerFromMonitoring.yml -t updateMonitoring`
> - Executes mandatory task to update vROPS configuration of SNOW plugin `dhc/manage$ ansible-playbook excludeAbsLayerFromMonitoring.yml -t updateJsonVrops`

# Postchecks

Postchecks should contain validation on two levels:

- HTTP Gateway
- ServiceNow

Please simulate any kind of alert in vROPs and validate if alert is captured by HTTP Gateway server and event/incident created in ServiceNow.

## Validate the HTTP Gateway logs

1. Login to server with domain account.
2. Navigate to location: `/opt/pubsubpy3.7/dpcop-pubsub-http-gw-master/pubsubhttpgateway`
3. Take a look on file `entrypoint.log`. It contains all application logs, you can run `tail -f entrypoint.log` command to have a live view on the file content.
4. Validate that there is no errors in logs and you see lines with patterns like:

```shell

SNOW return code: SIA-xxxx
[EVENTxxxxxxxxxx] Event created

```

The example of single full valid alert log can be found [here](#example-of-valid-alert-logs). Use it as reference to validate VCS monitoring as well.

## Validate ServiceNow Events

1. Login to proper SNOW instance.
2. Navigate to *Service Event Management* -> *Opened*
3. Filter out the event based on *Event ID* or *Affected CI* or any other field that suits for you. You can retrieve filter values from entrypoint.log file mentioned above.
4. Make sure event is created.

If you don't have access to *Service Event Management* please contact ServiceNow SME to validate it on behalf of you.

## Example of valid alert logs

```xml
2022-07-27 11:31:18,185 - backend - DEBUG - b'{"short_description":"Virtual machine CPU usage is at 100% for an extended period of time","comments":"New alert was generated 139.21.242.16 at Wed Jul 27 11:31:15 GMT 2022: ","caller_id":"vrops","category":"incident","subcategory":"performance","business_service":"DHC","contact_type":"mail","state":"1","hold_reason":"0","impact":"2","urgency":"2","priority":"2","assignment_group":"DHC","assigned_to":"1","severity":"1","upon_approval":"1","problem_id":"1","caused_by":"1","close_code":"1","close_notes":"2","resource_name":"fth01tss002","start_date":"Wed Jul 27 11:31:15 GMT 2022","resource_id":"a8012428-28ca-4431-bbec-0841afe98538","adapter_kind":"VMWARE","resource_kind":"VirtualMachine","criticality":"critical","health":"4.0","risk":"2.0","efficiency":"1.0","type":"Virtualization/Hypervisor","sub_type":"Performance","status":"Active","symptom_info":"Symptom Set - self\\nVirtual machine sustained CPU usage is 100% | fth01tss002 | a8012428-28ca-4431-bbec-0841afe98538 \\n CPU|Usage : 100.0... >= 100.0\\n\\n","alert_name":"Virtual machine CPU usage is at 100% for an extended period of time","host_name":"139.21.242.16","alert_id_key":"a2c8439b-d633-4959-b125-ee81c8bee0c0","alertDetailUrl":"https://139.21.242.16/ui/index.action#/object/all/a8012428-28ca-4431-bbec-0841afe98538/alertsAndSymptoms/alerts/a2c8439b-d633-4959-b125-ee81c8bee0c0","cmdb_ci":"fth01tss002"}'
2022-07-27 11:31:18,186 - VropsEvent - DEBUG - startDate Wed Jul 27 11:31:15 GMT 2022
2022-07-27 11:31:18,186 - VropsEvent - DEBUG - Detected Tenant: 1
2022-07-27 11:31:18,186 - VropsEvent - DEBUG - Detected Tenant not defined in entrypoint - using default FO for Management CIs.
2022-07-27 11:31:18,186 - VropsEvent - DEBUG - FO name set to: Siemens MMSA
2022-07-27 11:31:18,186 - VropsEvent - DEBUG - Querying vCenter fth01vcs001.siedhc01.next
2022-07-27 11:31:18,187 - VropsEvent - DEBUG - False
2022-07-27 11:31:18,188 - urllib3.connectionpool - DEBUG - Starting new HTTPS connection (1): fth01vcs001.siedhc01.next
2022-07-27 11:31:18,384 - urllib3.connectionpool - DEBUG - https://fth01vcs001.siedhc01.next:443 "POST /rest/com/vmware/cis/session HTTP/1.1" 200 None
2022-07-27 11:31:18,386 - VropsEvent - DEBUG - Successfully obtained vCenter bearer Token
2022-07-27 11:31:18,387 - urllib3.connectionpool - DEBUG - Starting new HTTPS connection (1): fth01vcs001.siedhc01.next
2022-07-27 11:31:18,439 - urllib3.connectionpool - DEBUG - https://fth01vcs001.siedhc01.next:443 "GET /rest/vcenter/vm?filter.names.1=fth01tss002 HTTP/1.1" 200 None
2022-07-27 11:31:18,440 - VropsEvent - DEBUG - Successfully obtained vCenter Vm:vm-1050
2022-07-27 11:31:18,441 - urllib3.connectionpool - DEBUG - Starting new HTTPS connection (1): fth01vcs001.siedhc01.next
2022-07-27 11:31:18,451 - urllib3.connectionpool - DEBUG - https://fth01vcs001.siedhc01.next:443 "GET /rest/vcenter/vm/vm-1050 HTTP/1.1" 200 None
2022-07-27 11:31:18,451 - VropsEvent - DEBUG - Successfully obtained Vm Id:50254da9-8e2c-02a3-fb04-adc2b5ef84e9and name:fth01tss002 under vCenter fth01vcs001.siedhc01.next
2022-07-27 11:31:18,452 - VropsEvent - DEBUG - Vm Ci fth01tss002 found under vCenter fth01vcs001.siedhc01.next
2022-07-27 11:31:18,452 - VropsEvent - DEBUG - Obtained Vm id:50254da9-8e2c-02a3-fb04-adc2b5ef84e9
2022-07-27 11:31:18,453 - urllib3.connectionpool - DEBUG - Starting new HTTPS connection (1): atosglobalcat.service-now.com
2022-07-27 11:31:18,700 - urllib3.connectionpool - DEBUG - https://atosglobalcat.service-now.com:443 "GET /api/now/table/cmdb_ci_vmware_instance?vm_instance_uuid=50254da9-8e2c-02a3-fb04-adc2b5ef84e9 HTTP/1.1" 200 None
2022-07-27 11:31:18,703 - VropsEvent - DEBUG - (SNOW) Successfully obtained CIID :SNC.G.C01.029198117
2022-07-27 11:31:18,703 - VropsEvent - DEBUG - Starting SOAP request to ServiceNow
2022-07-27 11:31:18,703 - VropsEvent - DEBUG - EVENT TYPE = PERFORMANCE
2022-07-27 11:31:18,704 - urllib3.connectionpool - DEBUG - Starting new HTTPS connection (1): atosglobalcat.service-now.com
2022-07-27 11:31:19,299 - urllib3.connectionpool - DEBUG - https://atosglobalcat.service-now.com:443 "POST /ServiceEventManagement.do?SOAP HTTP/1.1" 200 382
2022-07-27 11:31:19,301 - VropsEvent - DEBUG - Soap payload:                 <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:esb="http://esb.atos.net/services/ESBEventService/" xmlns:com="http://esb.atos.net/schemas/common" xmlns:even="http://esb.atos.net/schemas/event">
    <soapenv:Header />
    <soapenv:Body>
        <esb:createEvent>
            <header>
                <com:messageID></com:messageID>
                <com:srcApplicationID></com:srcApplicationID>
                <!--Optional:-->
                <com:authentication>
                    <com:userName>
          </com:userName>
                    <com:password>
          </com:password>
                </com:authentication>
                <!--Optional:-->
                <com:dstApplicationID>
        </com:dstApplicationID>
            </header>
            <statusNotificationSubscription>NEVER</statusNotificationSubscription>
            <event>
                <even:eventKey>
                    <even:eventID>ATF-DHC-1-vROPS-a2c8439b-d633-4959-b125-ee81c8bee0c0</even:eventID>
                    <even:eventSender>ATF-DHC-1</even:eventSender>
                </even:eventKey>
                <even:eventTime>
                    <even:timeOccured>2022-07-27T11:31:15+00:00</even:timeOccured>
                    <even:timeSent>2022-07-27T11:31:15+00:00</even:timeSent>
                </even:eventTime>
                <even:eventClass>
                    <even:eventType>PERFORMANCE</even:eventType>
                    <even:eventSenderType>ATF-DHC</even:eventSenderType>
                    <even:eventClass></even:eventClass>
                    <!--Optional:-->
                    <even:eventSeverity>critical</even:eventSeverity>
                    <!--Optional:-->
                    <even:eventHostname>fth01tss002</even:eventHostname>
                    <even:eventMessageText>Virtual machine CPU usage is at 100% for an extended period of time # VirtualMachine # Symptom Set - self
Virtual machine sustained CPU usage is 100% | fth01tss002 | a8012428-28ca-4431-bbec-0841afe98538
 CPU|Usage : 100.0... >= 100.0

</even:eventMessageText>
                </even:eventClass>
                <even:configItemID>
                    <even:id>SNC.G.C01.029198117</even:id>
                    <even:idType>CIID</even:idType>
                    <!--Optional:-->
                    <even:configItemSource>dhc://1</even:configItemSource>
                </even:configItemID>
                <even:troubleTicket>
                    <!--Optional:-->
                    <even:priority>Medium</even:priority>
                    <!--Optional:-->
                    <even:workgroup>
          </even:workgroup>
                    <!--Optional:-->
                    <even:category>Cloud.IaaS.DHC</even:category>
                </even:troubleTicket>
                <!--Optional:-->
                <even:additionalEventAttributes>
                    <!--Zero or more repetitions:-->
                    <even:attribute>
                        <even:name>FunctionalOrganization</even:name>
                        <even:value>Siemens MMSA</even:value>
                    </even:attribute>
                    <even:attribute>
                        <even:name>AlertDetailedUrl</even:name>
                        <even:value>https://139.21.242.16/ui/index.action#/object/all/a8012428-28ca-4431-bbec-0841afe98538/alertsAndSymptoms/alerts/a2c8439b-d633-4959-b125-ee81c8bee0c0</even:value>
                    </even:attribute>
                </even:additionalEventAttributes>
            </event>
        </esb:createEvent>
    </soapenv:Body>
</soapenv:Envelope>

2022-07-27 11:31:19,302 - VropsEvent - DEBUG - Response code: 200
2022-07-27 11:31:19,302 - VropsEvent - DEBUG - Response: <?xml version='1.0' encoding='UTF-8'?><SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><SOAP-ENV:Body><ns4:createEventResponse xmlns:S="http://schemas.xmlsoap.org/soap/envelope/" xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns2="http://esb.atos.net/schemas/common" xmlns:ns3="http://sia.atos.net/schemas/event" xmlns:ns4="http://esb.atos.net/services/ESBEventService/"><header><ns2:messageID/><ns2:srcApplicationID>atosglobalcatevent</ns2:srcApplicationID><ns2:dstApplicationID/></header><return><ns2:returnCode>SIA-0000</ns2:returnCode><ns2:description>OK</ns2:description><ns2:detail>[EVENT0051860442] Event created</ns2:detail></return></ns4:createEventResponse></SOAP-ENV:Body></SOAP-ENV:Envelope>
2022-07-27 11:31:19,302 - VropsEvent - DEBUG - SNOW return code: SIA-0000
2022-07-27 11:31:19,302 - VropsEvent - DEBUG - [EVENT0051860442] Event created
```

# Rollback

In order to proceed with rollback please follow steps:

1. Navigate to `/opt/pubsubpy3.7/dpcop-pubsub-http-gw-master/pubsubhttpgateway/` directory.
2. Remove following files:
    - `entrypoint.sh`
    - `backend.py`
    - `cloudevent.py`
3. Move files from `backup_files` directory into `pubsubhttpgateway` directory and rename it accordingly.
4. Restart the application by typing:

```shell

# ps -ef | grep 'unicorn' | grep -v grep | awk '{print $2}' | xargs -r kill -9
# ./validateHttpGw.sh

```
