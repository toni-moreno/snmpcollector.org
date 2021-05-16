---
title: "Measurement Groups"
description: "A Measurement Group is a collection  of measurements, and its main goal is to act as a \"Product\" or \"Device Model\" Template. Once defined should be easy creating new devices and only adding this measurement group. You can add as many measurements as you wish."
lead: "A Measurement Group is a collection  of measurements, and its main goal is to act as a \"Product\" or \"Device Model\" Template. Once defined should be easy creating new devices and only adding this measurement group. You can add as many measurements as you wish."
date: 2021-03-16T08:43:34+01:00
lastmod: 2021-03-16T08:43:34+01:00
draft: false
menu:
  docs:
    parent: "components"
weight: 170
toc: true
---

## View

Configured **Measurement Groups** are shown as table. This will appear every time you navigate from menu.

![Measurement Groups list](images/config_meas_groups_list.JPG)


## Add/Edit InfluxDB output databases

- **Add**: In the InfluxDB Servers list click on new button ![New Button](images/new_button.JPG)

- **Edit**: Click on InfluxDB edit's button ![Edit Button](images/edit_button.JPG)

The Create/Modify form will show up:

![New button](images/config_meas_groups_edit.JPG)

### Configuration Parameters

Type of settings|Config Parameter| Description
----------------|-----------------|-----------------
MeasurementGroups settings|ID| Text String that uniquely identify the measurement group recommended use the Product/Device Name/model as, should be unique in the database
||Measurements| A list of measurement id's
Extra settings|Description| A description text for the  measurement group.

### Multi edit in Measurement Groups

Multi edit has 1 option: Remove

1. Remove: Allows to remove all the selected items