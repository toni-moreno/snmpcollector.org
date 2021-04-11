---
title: "InfluxDB Servers"
description: "Defined InfluxDB Servers where collected metrics are stored."
lead: "Defined InfluxDB Servers where collected metrics are stored."
date: 2021-03-16T08:43:34+01:00
lastmod: 2021-03-16T08:43:34+01:00
draft: false
menu:
  docs:
    parent: "components"
weight: 110
toc: true
---


## View

Configured Influx Servers are shown as table. This will appear every time you navigate from menu.

{{< postimage "images/config_influx_servers_list.jpg" "influx servers list" >}}


## Add/Edit InfluxDB output databases

- **Add**: In the InfluxDB Servers list click on new button ![New Button](images/new_button.JPG)

- **Edit**: Click on InfluxDB edit's button ![Edit Button](images/edit_button.JPG)

The Create/Modify form will show up:

{{< postimage "images/config_influx_servers_edit.jpg" "New button" >}}

### Configuration Parameters

These are the following parameters, that let you create an HTTP based InfluxDB Connection

Type of Settings| Config Parameter| Description
-----------------|-----------------|-----------------
Server settings| ID| Text String that uniquely identify the influxdb connection
| | Host| The hostname/IP to connect
| | Port| The port to connect
| | Timeout| Connection timeout
Database settings| DB| InfluxDB database's Name
| | User| Username with `WRITE` privileges on selected InfluxDB database
| | Password| Password of related User
| | Retention Policy| Database retention policy name
| | Timestamp Precision| Time units of the Timestamp
Extra settings| User Agent| HTTP user agent sent to the database backend ( useful to debug input data from de backend view), if not set the agent will send as user agent _snmpCollector-`<InstanceID>`_ (from the [general] config.toml section)
| | Description| Some useful description to administrators

### Dummy Server 

Until the user doesn't set up any database, an internal `dummy server` is created to send data from all gathered devices discarding data at the end of poll.

### The "default" Server
SnmpCollector is able to send its own stats to InfluxDB: it will look for an InfluxDB Server named "default" to use as output backend, if it doesn't exist It will send over the `Dummy Server`.

(Pending define default and dummy influxservers)

### Multi edit in Influx Servers

Multi edit has 2 options: Remove and Change property

1. Remove: Allows to remove all the selected items

2. Change property: Allows to change the selected property of all the selected items. The properties are the same defined in the configuration.

Property| Type| Options/Values
----------------|----------------|----------------
Precision| Selector| h,m,s,ms,u,ns
Retention| Input| Retention policy name
Timeout| Input| "X" s
