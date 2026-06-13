# Table of Contents

<!-- TOC -->
- [Table of Contents](#table-of-contents)
- [List of Changes](#list-of-changes)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
- [Related Documents](#related-documents)
- [Enabling Inactive Host notification function in Log Insight](#enabling-inactive-host-notification-function-in-log-insight)
- [Using Inactive Host notification function in Log Insight](#using-inactive-host-notification-function-in-log-insight)

<!-- TOC -->

# List of Changes
  
| Version | Date       | Description      | Author       |
| ------- | ---------- | ---------------- | -------------|
| 0.1     | 11.10.2021 | First version    | Adam Szymczak |

## Introduction

### Purpose

Enable Inactive Host notification in Log Insight.

### Audience

- VCS Operations

### Scope

1. Setting up Inactive Host notification
2. How to use Inactive Host notification

# Related Documents

N/A

# Enabling Inactive Host notification function in Log Insight

1) Log in to **Log Insight** appliance using admin credentials
2) Go to **Administration** tab and then **Hosts** tab under **Management** category
3) On the screen that appears select **Inactive hosts notification**
4) Select period after which the alert should be sent, below there will be information how often is the inactivity checked
5) If the **Hosts** list includes items that not to be monitored for inactivity select **Inactive hosts notification whitelist** and add there items that should be monitored, if this option is not selected all items on the list will be checked for inactivity
6) After the configuration is done click **SAVE** button

![Figure 1](images/wiCheckLogInsightLogCollection/Configuration.png)

# Using Inactive Host notification function in Log Insight

This function works by counting time since last received event. It can be checked on **Hosts** page where the list of every host or appliance that sent logs to **Log Insight**. It also means that it can also monitor hosts that don`t have log agent installed and use **syslog** to push logs instead. When logs from one or more appliances/hosts are not received for period specified in configuration an alert in **vROps** is triggered. It will look like example alert below. When this alert is received engineer has to log in to **Log Insight** and under **Hosts** page check for which hosts/appliances alert threshold has been exceeded and check if log collection is working for them. If log collection is not functioning properly further steps have to be taken to fix the problem.

![Figure 2](images/wiCheckLogInsightLogCollection/vROpsAlert.png)
