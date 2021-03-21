---
title: "Custom filters"
description: "Create a filter directly from OID queries on related SNMP Device and related Measurement."
lead: "Create a filter directly from OID queries on related SNMP Device and related Measurement."
date: 2021-03-16T08:43:34+01:00
lastmod: 2021-03-16T08:43:34+01:00
draft: false
menu:
  docs:
    parent: "components"
weight: 190
toc: true
---

## View

Configured **Custom Filters** are shown as table. This will appear every time you navigate from menu.

{{< postimage "images/customfilter_overview.JPG" "Custom Filters" >}}


## Add/Edit Custom Filters

- **Add**: In the Custom Filters list click on new button ![New Button](images/new_button.JPG)

- **Edit**: Click on Custom Filter edit's button ![Edit Button](images/edit_button.JPG)

A modal will shown up with some steps that the user must complete:

### 1. Select related SNMP Device:

**_Note:_**: Only valid when creating a new Custom Filter

**_Note:_**: The user can change the related device clicking on `pen` on the modal top

Select the `SNMP Device` to do the query:

{{< postimage "images/customfilter_new_modal_init.JPG" "Select Device" >}}

### 2. Select related Measurement:

If it has connection available, select the Measurement Group and Measurement to load the indexed OID:

{{< postimage "images/customfilter_connectiondata_panel.JPG" "Connection data panel" >}}

### 3. Select values

Click on `Send Query` button and query values will shown. After that, the user can add values from query result and from custom values:
#### a. Query Result
Select the values on the checkbox

{{< postimage "images/customfilter_queryresult_panel.JPG" "Query Result" >}}



#### b. Custom Values

Click on `Add Values` and Custom values form will appear:

{{< postimage "images/customfilter_customitems_panel.JPG" "Query Result" >}}



### Configuration Parameters

 Config Parameter| Description
----------------|----------------
ID| Text String for each
Description| The measurement on which filter could be applied if selected in the device.
Items| Items to filter <ul><li>TagID - Value to filter</li><li>Alias to apply on filter</li></ul>


### Creating Measurement Filter

Once a Custom Filter is added, the user can create a new Measurement Filter and assign the related Measurement and Custom filter to it

{{< postimage "images/customfilter_newfilter_filled.JPG" "Measurement Filter" >}}
