---
title: "SNMP Devices"
description: "The core component: SNMP Devices. The user will define Devices to poll data from using SNMP protocol."
lead: "The core component: SNMP Devices. The user will define Devices to poll data from using SNMP protocol."
date: 2021-03-16T08:43:34+01:00
lastmod: 2021-03-16T08:43:34+01:00
draft: false
menu:
  docs:
    parent: "components"
weight: 195
toc: true
---

# SNMP Devices

The core component: SNMP Devices. The user will define Devices to poll data from using SNMP protocol.

## View

Configured **SNMP Devices** are shown as table. This will appear every time you navigate from menu.

{{< postimage "images/config_snmp_devices_list.JPG" "" >}}

## Add/Edit SNMP Devices

- **Add**: In the SNMP Devices list click on new button ![New Button](images/new_button.JPG)

- **Edit**: Click on SNMP Devices edit's button ![Edit Button](images/edit_button.JPG)

The Create/Modify form will show up:

{{< postimage "images/config_snmp_devices_edit_1.jpg" "" >}}
<br/>



### Configuration Parameters

Type of settings|Config Parameter| Description
----------------|----------------|----------------
Core settings|ID| Text String that uniquely identify the device , should be unique in the config db, it can be hostname, serial number or any other text id
||Active| let device begin gathering process on agent boot process
Device settings|Host| the network direction the agent will  use to do snmp connection , could be IP or fqdn name
||Port|the network port where the agent will use to do snmp connections
||Timeout| sets de timeout for each SNMP query
||Retries| sets the number of retries to attempt within Timeout 
||Alternate System OID's| Set OID to get like MIB-2::System Info for non MIB-2 based devices
Debug settings|LogLevel|enable use select verbosity of the device log
||SnmpDebug|if this option is set all snmp low level protocol queries with detailed info on this device will be written on a file with name "snmpdebug_XXXXXX.log"
Polling settings|Snmpversion| could be any of these [1,2c,3]
||Disable snmpbulk| if true the bulk feature won't be used ( these feature is needed to query IBM XIV disk arrays, which has a  snmpv2/v3 agent with a buggy bulk support)
||MaxRepetitions| set The MaxRepetitions parameter for BULKWALK SNMP queries
||Community| the snmp version 2 community
||v3seclevel|(only for snmpv3) define the level of security needed for the connection valid values are.<br/><br/><ul><li>NoAuthNoPriv</li><li>AuthNoPriv</li><li>AuthPriv</li></ul>
||v3authuser|(snmpv3 only) the username that will establish the snmp query
||v3authpass|(snmpv3 only) the authentication password
||v3authprot|(snmpv3 only) the Authentication Protocol  values should be any of "MD5", "SHA"
||v3privpass|(snmpv3 only) Privacy password
||v3privprot|(snmpv3 only) Privacy Protocol values should be any of [ "DES", "AES"]
||v3ContextEngineID|SNMPV3 ContextEngineID in ScopedPDU (equivalent to the net-snmp -E paramenter)
||v3ContextName|SNMPV3 ContextName in ScopedPDU ( equivalent to the net-snmp -n parameter)
||Freq| Frequency of polling in seconds ( default 30 sec if not set)
||UpdateFltFreq| Number pof complete polls that agent will wait before perform snmp table index/filter updates , the final update time will be  Freq*FilterUpdate ( in seconds) Default 60 ( 1h elapsed time)
||ConcurrentGather|Open a new SNMP Connection for each measurement and send concurrent queries over the device.
Data settings|InfluxDB Server|let you select over which database you will send data.
||DeviceTagName| Tag name the agent will send to the output db  (default : "device"), could be any of the other more explicit tagname depending on the device type, context , by example  (router,switch,firewall,dns..etc)
||DeviceTagValue| could be one of these:<br><ul><li><strong>id</strong>: will send as device tag the configured ID for this measurement</li><li><strong>Host</strong>will send as device tag data configured in the Host configuration (name or IP).
||ExtraTags|an array of tags for this device that will be sent on all its measurements.
||Override Device Vars|List of var catalog to override the default set value
||Measurement Groups|an array of Measurement Group ID.
||Measurement Filters| an array for configured filters
Extra settings|Description| add here all important information over this device

### Test Connection

When editing or adding a SNMPDevice there is the option to send a query and check its connectivity and send OID queries from already defined components.

To access on the Test Connection Modal, click on `Test Connection`

{{< postimage "images/config_test_connection_list.JPG" "" >}}

The user can select the `OID Source`:

{{< postimage "images/config_test_connection_components.JPG" "" >}}


The OID related on selected component will load and the user will be able to send query to see those results:

{{< postimage "images/config_test_connection_oid.JPG" "" >}}

{{< postimage "images/config_test_connection_result.JPG" "" >}}


### Filter Connection

As the user is editing or adding a new Device it a Custom Filter can be created directly from its form.
To access, click on "Filter Connection" button and the form will shown up.

For more information, check Component: Custom Filters

### Multi edit in SNMP Devices

Multi edit has 3 options: Remove,Change property or Append Property

1. Remove: Allows to remove all the selected items

2. Change property: Allows to change the selected property of all the selected items. The properties are the same defined in the configuration.

Property| Type| Options/Values
----------------|----------------|----------------
Active| Selector| True /False
LogLevel| Selector | Panic/ Fatal/ Error/ Warning/ Info /Debug 
Concurrent Gather| Selector| True /False
Disable Bulk| Selector| True /False
SNMPDebug| Selector| True /False
Timeout| Input| Time in seconds
Retries| Input | Any number
Freq| Input| Any number in Hz
Max Repetitions| Input| Any number 
Device Tag Name| Input| Any string
MeasurementGroup| Selector| List of all measurement groups
MeasFilters| Selector| List of all the filters
ExtraTags| Input| Any tag (string)

3. Append Property: Allows to append a new measurement, filter o extra tags in the selected devices.

Property| Type| Options/Values
----------------|----------------|----------------
MeasurementGroup| Selector| List of all measurement groups
MeasFilters| Selector| List of all the filters
ExtraTags| Input| Any tag (string)

