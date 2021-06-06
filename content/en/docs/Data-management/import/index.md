---
title: "Import"
description: "Create a filter directly from OID queries on related SNMP Device and related Measurement."
lead: "Create a filter directly from OID queries on related SNMP Device and related Measurement."
linkTitle: "Import"
draft: false
menu:
  docs:
    parent: "Data Management"
weight: 41
toc: true
---

# IMPORTING A FILE

To import a file the option "Import" in the menu will be used.

![Menu import](/webUI/DataManagement/import-export-select.jpg)

This option allows the user to select a file ( in this case a JSON file) and select two options:

![Import file](/webUI/DataManagement/import-file.jpg)

* AutoRename -> It allows to change the name of the file, is useful if u want to copy a device, measurement, etc and rename it.
* Overwrite -> It allows to overwrite the current file in the SNMP collector with the same name as the imported file.

By default both options are false.

If one of the options is true, the other will be locked.



