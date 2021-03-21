---
title: "Variable Catalog"
description: "The variable catalog let us to define variable names ( and its default values if not redefined before) to use on STRINGEVAL metric types, These variables could be redefined on each device maintaining the metric formula definition across devices."
lead: "The variable catalog let us to define variable names ( and its default values if not redefined before) to use on STRINGEVAL metric types, These variables could be redefined on each device maintaining the metric formula definition across devices."
date: 2021-03-16T08:43:34+01:00
lastmod: 2021-03-16T08:43:34+01:00
draft: false
menu:
  docs:
    parent: "components"
weight: 100
toc: true
---


## View
Available Variables are shown as table. This will appear every time you navigate from menu.

{{< postimage "images/variable_catalog_edit.JPG" "" >}}


## Add/Edit Variables to the catalog

- **Add**: In the catalog list click on new button ![New Button](images/new_button.JPG)

- **Edit**: In the catalog list click the edit's button ![Edit Button](images/edit_button.JPG)

The Create/Modify form will show up:

{{< postimage "images/variable_catalog_edit.JPG" "Edit variables" >}}


### Configuration Parameters

These are the following parameters, that let you create an usable Variable 

Type of settings|Config Parameter| Description
----------------|----------------|----------------
Variable settings| ID| Text String that uniquely identify the variable ( and will be placed on STRINGEVAL formulas)
| |type|The type of the variable ( STRINGEVAL could be used to generate numbers as well as strings, as the evaluation engine let us a lot of expresions see !  [EVALUATION MANUAL](https://github.com/Knetic/govaluate/blob/master/MANUAL.md)
| |Value| The default value if not redefined elsewhere 
Extra settings|Description| Some useful description to administrators

### Variable Redefinition

All variables could be redefined on the host definition as "Device Variables" with its custom value , if not redefined but used on any of the measurements defined in the device the default value will be taken.

### Multi edit in Variable Catalog

Multi edit has 2 options: Remove and Change property

1. Remove: Allows to remove all the selected items

2. Change property: Allows to change the selected property of all the selected items. The properties are the same defined in the configuration.

Property| Type| Options/Values
----------------|----------------|----------------
Type| Input| Type of the new variable
Value| Input| Value of the new variable

