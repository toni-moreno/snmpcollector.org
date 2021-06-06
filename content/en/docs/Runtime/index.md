---
title: "Runtime"
description: "Runtime Options."
linkTitle: "Runtime"
lead: ""
date: 2020-10-13T15:21:01+02:00
lastmod: 2020-10-13T15:21:01+02:00
draft: false
images: []
menu: 
  docs:
    parent: "runtime"
weight: 5
toc: true
---

SNMPCollector has an embedded online device state and data measurement viewer, which help us to monitor how data has been gathered for each device.
On the device list you will see if device is active ![devactive](/webUI/Runtime/active_button.JPG) or have been stoped ![devnotactive](/webUI/Runtime/desactived_button.JPG) by the administrator. You can also see if there is any connectivity problems ![notconnected](/webUI/Runtime/notConnected_button.JPG)

![Device List](/webUI/Runtime/runtime_device_listt.JPG)


## System description

![Host Info](/webUI/Runtime/runtime_device_info_operations.JPG)

## Runtime Operations

Operation|Description
---------|-----------
SNMP State | Enable or disable online the selected SNMP Device
Force Gather| Execute a Complete Gathering process,  useful for testing on not active devices or force again
SNMP Debug | Enable or disable SNMP Debugging
Log Level | Change Log level online
Force Flt Update | Force filter update on the next poll
Download Log File | Download the log file related with device
Force SNMP Reset (soft) | only restarts snmp connection for current established measurements and  with current indexed states an filter states. 
Force SNMP Reset (hard) | stop all snmp communications and restart them again, remap again all measurement map, indexes in the same way a restart is done.


## Measurement online data

![Meas Online data](/webUI/Runtime/runtime_device_measurement_data_explorer.JPG)


Tooltip detail of one metric:

![Meas Detailed Info](/webUI/Runtime/runtime_metric_definition_info.JPG)

Tooltip detail of one metric value:

![Meas Detailed Info](/webUI/Runtime/runtime_device_data_tooltip_detail.JPG)