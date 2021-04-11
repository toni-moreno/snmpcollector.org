---
title: "Measurement Filters"
description: "Filters allows to filter collected data from different Measurements. The filters can be configured using a file, OID Condition or Custom filter."
lead: "Filters allows to filter collected data from different Measurements. The filters can be configured using a file, OID Condition or Custom filter.."
date: 2021-03-16T08:43:34+01:00
lastmod: 2021-03-16T08:43:34+01:00
draft: false
menu:
  docs:
    parent: "components"
weight: 180
toc: true
---

## View

Configured Measurement Filters are shown as table. This will appear every time you navigate from menu.

![SNMP Metric List](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/images/webUI/MeasurementFilters/config_meas_filters_list.JPG)

## Add/Edit Measurement Filters

- **Add**: In the Measurement Filters list click on new button ![New Button](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/images/webUI/Tables/new_button.JPG)

- **Edit**: Click on Measurement Filter edit's button ![Edit Button](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/images/webUI/Tables/edit_button.JPG)

The Create/Modify form will show up:

![Create/Modify SNMP Metrics](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/images/webUI/MeasurementFilters/config_meas_filters_edit.JPG)

### Configuration Parameters

Type of settings| Config Parameter| Description
----------------|-----------------|-----------------
Core settings|ID| Text String for each
||MeasurementID| The measurement on which filter could be applied if selected in the device.
Filter settings|Filter Type| <ul><li>OID Condition</li><li>File Condition</li><li>Custom Filter</li></ul>
||File Name|Depending on Filter Type: <ul><li>File: the file name by example ("my_filer_file_for_device_xxxxx.txt")</li><li>OID Condition: ID of defined OID Condition </li><li>Custom Fiter: ID of defined Custom Filter</li></ul>
||Enable Alias|(Only apply on the File Filter or Custom Filter type) after filtering it takes 2on column if defined as a TAG for the measurement better than the original name (in the first column)
Extra settings|Description|Useful description for the administrator
### Multi edit in Measurement Filters 

Multi edit has 1 option: Remove

1. Remove: Allows to remove all the selected items