---
title: "Measurements"
description: "[Measurements](https://docs.influxdata.com/influxdb/v1.1/concepts/glossary/#measurement) are the basic Influxdb structure let us group series of the  same type/origin."
lead: "[Measurements](https://docs.influxdata.com/influxdb/v1.1/concepts/glossary/#measurement) are the basic Influxdb structure let us group series of the  same type/origin."
date: 2021-03-16T08:43:34+01:00
lastmod: 2021-03-16T08:43:34+01:00
draft: false
menu:
  docs:
    parent: "components"
weight: 160
toc: true
---


<!-- Images references -->
<!-- https://github.com/toni-moreno/snmpcollector/blob/ -->


[New Button]: images/new_button.JPG "New Button"
[Edit Button]: images/edit_button.JPG "Edit Button"

[add_button]: images/add_button.jpg "Add button"
[remove_button]: images/remove_button.jpg "Remove button"
[promote_button]: images/promote_button.jpg "Promote button"
[demote_button]: images/demote_button.jpg "Demote button"


# Measurements

When data are gathered from  SNMP devices we can group them together as we wish when snmp data are scalar objects but is mandatory  group snmp tabular objects in the same measurement.

## View

Configured **Measurements** are shown as table. This will appear every time you navigate from menu.

{{< postimage "images/config_measurements_list.JPG" "SNMP Measurements List" >}}

## Add/Edit Measurements

- **Add**: In the Measurements list click on new button ![New Button]

- **Edit**: Click on Measurement edit's button ![Edit Button]

The Create/Modify form will show up:


{{< postimage "images/config_measurements_edit.JPG" "Create/Modify Measurements" >}}

### Multi edit in Measurements

Multi edit has 2 options: Remove and Change property

1. Remove: Allows to remove all the selected items

2. Change property: Allows to change the selected property of all the selected items. The properties are the same defined in the configuration.

Property| Type| Options/Values
----------------|----------------|----------------
IndexTag| Input| Tag Name (string)
IndexAsValue| Selector| True/False


### Configuration Parameters

#### Core Settings
Config Parameter| Description
--- | ---
ID | Text String that uniquely identify the measurement recommended use the MIB Name prefixed/suffixed with some other string data we can easily identify (as manufacturer or model) that will help us to look in the Measurement config section, should be unique in the database
Name | This will be the Measurement name created in the Database when sending data, will let us to query data with  "SELECT * from <Name>..."

#### Measurement Settings

The behaviour of a measurement and its related metrics is given by the field `GetMode`


#### Direct Value

The `Value` type measurement doesn't go over any OID and it won't generate any tag to identify its metrics
Only non-indexed metrics should be added

Config Parameter| Description
--- | ---
Fields | A list of MetricID which will be part of this measurement and what to do with the value. On `Value` measurements only metrics that are not-indexed should be added
Report Fields | All metrics can be configured to be sent or not to the backend:<br><ul><li><strong>Report always:</strong>in this mode the vale gathered for this Metric by the agent will be sent to the output db</li><li><strong>Never report:</strong>in this mode the value gathered for this metric will not be sent, it has sense when we need this value as a input parameter for computed metrics (metrics of type String-Eval) </li> <li><strong>Report if non zero:</strong>in this  mode the value is not sent if the result cooked or captured is `0`</li><ul>

#### Indexed with direct TAG

The `Indexed with direct Tag` measurement iterates over a table (`IndexOID`) and maps the index and result to be used as tags.
All attached metrics will be identified by that index, so all the metrics should be, or share, on the same table/index

Config Parameter| Description
--- | ---
IndexOID | (only required for SNMP Table based data) The OID will be queried to get row Tag Values of the table. Only metrics that shares the same table/index should be added
IndexTag | (only required for SNMP Table based data) the tag name will be sent to distinguish among different rows
IndexTagFormat|(only required for SNMP Table based data) enable custom format for TAGS based on parameters from values and indexes from IndexOID (parameters as `$IDX1` and `$VAL1`) <br> Default format is `$VAL1`.<br/> example: `[$IDX1]-$VAL1` will be sent as tag a "[1]-eth0 (see IndexTagFormat below for more detailed info)"
Fields | A list of MetricID which will be part of this measurement and what to do with the value. On `Value` measurements only metrics that are not-indexed should be added
Report Fields | All metrics can be configured to be sent or not to the backend:<br><ul><li><strong>Report always:</strong>in this mode the vale gathered for this Metric by the agent will be sent to the output db</li><li><strong>Never report:</strong>in this mode the value gathered for this metric will not be sent, it has sense when we need this value as a input parameter for computed metrics (metrics of type String-Eval) </li> <li><strong>Report if non zero:</strong>in this  mode the value is not sent if the result cooked or captured is `0`</li><ul>

Example of valid tables:

{{< postimage "images/schema_indexed.jpg" "Indexed Schema" >}}

```bash
Measurement Config: 
Index OID: ifXTable.IfName - 1.3.6.1.2.1.31.1.1.1.1

Index Process:
1.3.6.1.2.1.31.1.1.1.1[.1] = `eth0`
1.3.6.1.2.1.31.1.1.1.1[.2] = `eth1`
1.3.6.1.2.1.31.1.1.1.1[.3] = `eth2`
1.3.6.1.2.1.31.1.1.1.1[.4] = `eth3`
1.3.6.1.2.1.31.1.1.1.1[.5] = `eth4`


Metrics:
ifXTable.ifHCInOctets - 1.3.6.1.2.1.31.1.1.1.6
1.3.6.1.2.1.31.1.1.1.6.[1:eth0] = val1
1.3.6.1.2.1.31.1.1.1.6.[2:eth1] = val2
1.3.6.1.2.1.31.1.1.1.6.[3:eth2] = val3
1.3.6.1.2.1.31.1.1.1.6.[4:eth3] = val4
1.3.6.1.2.1.31.1.1.1.6.[5:eth4] = val5

ifXTable.ifHCInOctets - 1.3.6.1.2.1.31.1.1.1.10
1.3.6.1.2.1.31.1.1.1.10.[1:eth0] = val1'
1.3.6.1.2.1.31.1.1.1.10.[2:eth1] = val2'
1.3.6.1.2.1.31.1.1.1.10.[3:eth2] = val3'
1.3.6.1.2.1.31.1.1.1.10.[4:eth3] = val4'
1.3.6.1.2.1.31.1.1.1.10.[5:eth4] = val5'
```


#### Indexed with indirect TAG

The `Indexed with indirect TAG` measurements iterates over a table that has as value the related index to retrieve the tag (an indirect index). The related table will be retrieved and used as tags.
All attached metrics will be identified by the `IndexOID`, so all the metrics should be, or share, on the same table/index

Config Parameter| Description
--- | ---
IndexOID | (only required for SNMP Table based data) The OID will be queried to get row Tag Values of the table. Only metrics that shares the same table/index should be added
IndexTag | (only required for SNMP Table based data) the tag name will be sent to distinguish among different rows
TagOID|(only required for SNMP indirect Tag based data) the OID will be queried to get TAG value names from other table.
IndexTagFormat|(only required for SNMP Table based data) enable custom format for TAGS based on parameters from values and indexes from IndexOID ( parameters as `$IDX1` and `$VAL1`) or TagOID when measurement is defined as indirect indexes defined ( parameters `$IDX2` and `$VAL2`). <br/> Default format is `$VAL2`.<br/> example: `[$IDX1]-$VAL1-$VAL2` will be sent as tag a `[1]-4-eth0` (see IndexTagFormat below for more detailed info)"
Fields | A list of MetricID which will be part of this measurement and what to do with the value. On `Value` measurements only metrics that are not-indexed should be added
Report Fields | All metrics can be configured to be sent or not to the backend:<br><ul><li><strong>Report always:</strong>in this mode the vale gathered for this Metric by the agent will be sent to the output db</li><li><strong>Never report:</strong>in this mode the value gathered for this metric will not be sent, it has sense when we need this value as a input parameter for computed metrics (metrics of type String-Eval) </li> <li><strong>Report if non zero:</strong>in this  mode the value is not sent if the result cooked or captured is `0`</li><ul>


{{< postimage "images/schema_indexed_it.jpg" "schema indexed it" >}}

Example of valid tables:

```bash
Measurement Config:
IndexOID: cpmCPUTotalPhysicalIndex - 1.3.6.1.4.1.9.9.109.1.1.1.1.2
TagOID: entPhysicalTable.entPhysicalName - 1.3.6.1.2.1.47.1.1.1.1.7

Index Process:

Index OID:
1.3.6.1.4.1.9.9.109.1.1.1.1.2[.1] = `7051`
1.3.6.1.4.1.9.9.109.1.1.1.1.2[.2] = `7052`
1.3.6.1.4.1.9.9.109.1.1.1.1.2[.3] = `7053`
1.3.6.1.4.1.9.9.109.1.1.1.1.2[.4] = `7054`
1.3.6.1.4.1.9.9.109.1.1.1.1.2[.5] = `7055`

1.3.6.1.2.1.47.1.1.1.1.7[.7051] = `cpu R0/0`
1.3.6.1.2.1.47.1.1.1.1.7[.7052] = `cpu R1/0`
1.3.6.1.2.1.47.1.1.1.1.7[.7053] = `cpu R2/0`
1.3.6.1.2.1.47.1.1.1.1.7[.7054] = `cpu R3/0`
1.3.6.1.2.1.47.1.1.1.1.7[.7055] = `cpu R4/0`

Index result:
[.1:cpu R0/0]
[.2:cpu R1/0]
[.3:cpu R2/0]
[.4:cpu R3/0]
[.5:cpu R4/0]

Metrics:

cpmCPUTotalTable.cpmCPUTotal1minRev - .1.3.6.1.4.1.9.9.109.1.1.1.1.7
.1.3.6.1.4.1.9.9.109.1.1.1.1.7.[1:cpu R0/0] = val1
.1.3.6.1.4.1.9.9.109.1.1.1.1.7.[2:cpu R1/0] = val2
.1.3.6.1.4.1.9.9.109.1.1.1.1.7.[3:cpu R2/0] = val3
.1.3.6.1.4.1.9.9.109.1.1.1.1.7.[4:cpu R3/0] = val4
.1.3.6.1.4.1.9.9.109.1.1.1.1.7.[5:cpu R4/0] = val5

cpmCPUTotalTable.cpmCPUTotal5minRev - .1.3.6.1.4.1.9.9.109.1.1.1.1.8
.1.3.6.1.4.1.9.9.109.1.1.1.1.8.[1:cpu R0/0] = val1'
.1.3.6.1.4.1.9.9.109.1.1.1.1.8.[2:cpu R1/0] = val2'
.1.3.6.1.4.1.9.9.109.1.1.1.1.8.[3:cpu R2/0] = val3'
.1.3.6.1.4.1.9.9.109.1.1.1.1.8.[4:cpu R3/0] = val4'
.1.3.6.1.4.1.9.9.109.1.1.1.1.8.[5:cpu R4/0] = val5'
```

#### Indexed with multiple indirect TAG

The `Indexed with multiple indirect TAG` measurements iterates N times over different tables to be retrieving the values that relates those tables until the tag is retrieved. The final related table will be retrieved and used as tags.
All attached metrics will be identified by the `IndexOID`, so all the metrics should be, or share, on the same table/index

Config Parameter| Description
--- | ---
IndexOID | (only required for SNMP Table based data) The OID will be queried to get row Tag Values of the table. Only metrics that shares the same table/index should be added. The final index will contain the first queried indexes of the table
IndexTag | (only required for SNMP Table based data) the tag name will be sent to distinguish among different rows
Multiple TagOID| The multiples OIDs **in order** that will be queried to retrieve the tag value. The value will be extracted and formatted from the last queried table. Each `MultipleTagOID` has: <ul><li>TagOID: the OID will be queried to get the value to iterate over the next one</li><li>Index Format: the index format using `IndexTagFormat` syntax to allow match and keep iterating over the tables</li></ul> See examples section for an extended example of multiple tagoid. **TagOIDs must be defined with order**
IndexTagFormat|(only required for SNMP Table based data) enable custom format for TAGS based on parameters from values and indexes from IndexOID ( parameters as `$IDX1` and `$VAL1`) or TagOID when measurement is defined as indirect indexes defined ( parameters `$IDX2` and `$VAL2`). <br/> Default format is `$VAL2`.<br/> example: `[$IDX1]-$VAL1-$VAL2` will be sent as tag a `[1]-4-eth0` (see IndexTagFormat below for more detailed info)"
Fields | A list of MetricID which will be part of this measurement and what to do with the value. On `Value` measurements only metrics that are not-indexed should be added
Report Fields | All metrics can be configured to be sent or not to the backend:<br><ul><li><strong>Report always:</strong>in this mode the vale gathered for this Metric by the agent will be sent to the output db</li><li><strong>Never report:</strong>in this mode the value gathered for this metric will not be sent, it has sense when we need this value as a input parameter for computed metrics (metrics of type String-Eval) </li> <li><strong>Report if non zero:</strong>in this  mode the value is not sent if the result cooked or captured is `0`</li><ul>

**Mutiple TagOID manipulation**

Use the following button to add a new TagOID ![add_button]

The following buttons allow to reorder and delete defined TagOIDs:

- Remove: ![remove_button]
- Promote: ![promote_button]
- Demote: ![demote_button]


{{< postimage "images/multi_tagoid_example.jpg" "Multi Tag OID Example" >}}


**Example of valid tables**:

{{< postimage "images/schema_indexed_mit.jpg" "" >}}

```bash
Measurement Config:
IndexOID: indexedExample - 1.2.3.5.1
MultiTagOID
        0: - TagOID: example1 - 1.2.3.6.1
           - IndexFormat: ""

        1: - TagOID: example2 - 1.2.3.7.1
           - IndexFormat: ""

        2: - TagOID: example3 - 1.2.3.8.1
           - IndexFormat: ""


Index Process:
IndexOID - indexExample:

1.2.3.5.1[.1] = `7051`
1.2.3.5.1[.2] = `7052`
1.2.3.5.1[.3] = `7053`
1.2.3.5.1[.1] = `7054`
1.2.3.5.1[.2] = `7055`

TagOID 0 - example1

1.2.3.6.1[.7051] = `5001`
1.2.3.6.1[.7052] = `5002`
1.2.3.6.1[.7053] = `5003`
1.2.3.6.1[.7054] = `5004`
1.2.3.6.1[.7055] = `5005`

TagOID 1 - example2


1.2.3.7.1[.5001] = `601`
1.2.3.7.1[.5002] = `602`
1.2.3.7.1[.5003] = `603`
1.2.3.7.1[.5004] = `604`
1.2.3.7.1[.5005] = `605`

TagOID 2 - example3

1.2.3.8.1[.601] = `label0`
1.2.3.8.1[.602] = `label1`
1.2.3.8.1[.603] = `label2`
1.2.3.8.1[.604] = `label3`
1.2.3.8.1[.605] = `label4`

Index result:
[.1:label1]
[.2:label2]
[.3:label3]
[.4:label4]
[.5:label5]

Metric:

myMetricExample - 1.2.3.10.1

1.2.3.10.1.[1:label1] = val1
1.2.3.10.1.[2:label2] = val2
1.2.3.10.1.[3:label3] = val3
1.2.3.10.1.[4:label4] = val4
1.2.3.10.1.[5:label5] = val5

myMetricExample - 1.2.3.10.2

1.2.3.10.1.[1:label1] = val1'
1.2.3.10.1.[2:label2] = val2'
1.2.3.10.1.[3:label3] = val3'
1.2.3.10.1.[4:label4] = val4'
1.2.3.10.1.[5:label5] = val5'
```

#### Multiple Indexes

The multiple indexes measurement allows the user to define different internal measurements that will create the map of index/value and can have dependencies to share its information between them
To see an example go to examples section

Config Parameter| Description
--- | ---
Multi Index Measurements | The array of indexes measurements (direct, indirect and multi indirect) to create a complex index with tags embebbed
Result | Custom final index that will be used to match fields OIDs. The syntax is explained on section below

Each `Multi Index Measurement` can be identified by its index (position in array)

The options available on the `Multi Index Measurements` are the same as defined above, depending of the type (`GetMode`) and the added one.

Config Parameter| Description
--- | ---
Index Label | The internal name of the index
Index Description | The internal description of the index
Index Dependency | The dependency syntax to heridate tags from other index based on the following syntax: `IDX{<index>};DOT[NN:MM];FILL(XXX)|SKIP`. The syntax is explained on section below


**Mutiple Index manipulation**

Select the type of Index to add a new one, click on the following button to add a index ![add_button]. 
Available indexes are:

- (snmp Table) Indexed with direct TAG - indexed
- (snmp Table) Indexed with indirect TAG - indexed_it
- (snmp Table) Indexed with multi indirect tag - indexed_mit

{{< postimage "images/multi_index_options.jpg" "Multi Index Options" >}}


The following buttons allow to reorder and delete defined Multi Indexes:

- Remove: ![remove_button]
- Promote: ![promote_button]
- Demote: ![demote_button]


{{< postimage "images/multi_index_example.jpg" "Multi Index Example" >}}


#### Multi Indexes: Dependencies and Result

Multi Indexes allows the user to define several internal measurements to retrieve different tags and identifies and build a custom index to use it on metrics


{{< postimage "images/schema_indexed_multiple.jpg" "Multiple Indexed Schema" >}}

**Dependency**

The dependency system between indexes allows the index to heridate already retrieved tags from other indexes and attach them to the current one
The syntax follows as:
```
IDX{IM};DOT[XX:YY];SKIP
```

If the user defines only `IDX{IM}`, a FILL("") strategy will be applied with an entire index match 

The `IDX{IM}` references to another index, based on its position of the array. Example:

```bash
Index 0 |   [.1] = "eth0"
        |
        |   Index Tag = portName
--------
Index 1 |   [.1] = up
        |
        |   Index Tag = "ifStatus"
        |   Dependency IDX{0}
        |       --> [.1] = "up|eht0" [ifStatus, portName]
```

The `DOT[XX:YY]` allows to change the check option to heridate tags from another index, this is useful if the tables doesn't have the same dimension, but it shares a common index. Example:

```bash
Index 0 |   [.1] = "eth0"
        |
        |   Index Tag = "portName"
--------
Index 1 |   [.1.2] = up
        |
        |   Index Tag = "ifStatus"
        |   Dependency IDX{0};DOT[0:0];FILL()
        |       --> [1.1] = "up|eht0" [ifStatus, portName]
```

The `FILL(XX)|SKIP` section defines the strategy of non match index:
- The `FILL(XX)` allows the user to define a custom tag value (`XXX`) if the dependency index has no the current index on it.
- The `SKIP` deletes the current point if it doesn't match with the current index

```bash
Index 0 |   [.1] = "eth0"
---------
Index 1 |   [.1.1] = "up"
        |   [.2.1] = "down"
        |    
        |   Dependency: IDX{0};DOT[0:0];FILL(no value present)
        |       --> [1.1] = "up|eth0" [ifStatus, portName]
        |       --> [2.1] = "down|no value present"
```

```
Index 0 |   [.1] = "eth0"
---------
Index 1 |   [.1.1] = "up"
        |   [.2.1] = "down"
        |
        |   Index Tag = "ifStatus"
        |   Dependency: IDX{0};DOT[0:0];SKIP
        |       --> [1.1] = "up|eth0" [ifStatus, portName]
        |       < DELETED [2.1]>
```

**Result**

The result allows the user to create a totally custom index to be set up with attached fields.
The syntax follows as:

```bash
IDX{IM};DOT[XX:YY]].<number>.<IDX{IM};DOT[XX:YY]>.<...>
```

If several IDX{IM} are found, it will create a big index using the scalar product between them heridating its tags

```bash
Index 0 |   [.1] = "eth0"
        |   [.2] = "eth1"
        |   <No dependency configured>
---------
Index 1 |   [.3] = "up"
        |   [.4] = "down"
        |   <No dependency configured>
```

Result: `IDX{0}.IDX{1}`

```bash
Final Index | [.1.3] = "eth0 | up" 
            | [.1.4] = "eth0 | down"
            | [.2.3] = "eth1 | up"
            | [.2.4] = "eth1 | down"
```

Result: `.35.IDX{0}`:

```bash
Final Index | [.35.1] = "eth0" 
            | [.35.1] = "eth0"
```

Result: `.35.IDX{1}.42`:

```bash
Final Index | [.35.3.42] = "up" 
            | [.35.3.42] = "down"
```

## Notes

---

### IndexTagFormat Definition

Enable user define a custom format for TAGS based on parameters from values and indexes from IndexOID or TagOID when measurement is defined as indirect indexes defined

Current variable inputs are only IDX1,VAL1 for direct indexed measurements and also IDX2,VAL2 when indirect indexed measurements.

To refer to these variables you could prepend a '$' to the variable name. If not definition exist,default format will be $VAL1 for direct indexed tables and $VAL2 for indirect indexed.

User can also take parts of each variable and apply transformation to get desired tag name by only use the following  syntax

`${VAR|SELECTOR|TRANSFORMATOR}`

* VAR : the variable source, could be referenced several times in the same definition
* SELECTOR: (by default the complete variable string) will select a bit of the VARIABLE, could be void string and the default value will be selected
* TRANSFORMATOR (by default STRING) will apply a transformation on the previously selected bit of variable, could be void string and the default value will be selected

$VAR will be equivalent to ${VAR|ALL|STRING} and also to ${VAR||}

#### Defined selectors 
* __ALL__: complete string
* __DOT[X:Y]__ : splits variable in an array of numbers and select only from the Xth to the Yth value, if X is omitted 0 will be chosen , if  Y is omitted the last element on the splitted array will be chosen , in this mode DOT[0:] is equivalent to ALL. Counter always begins from 0 and both X and Y position will be included in the final selection
* __REGEX/regex-expression/substitution-expression/__: (since 0.7.3) Regex based Substitution pattern , each section is described in the following table

regex section|meaning
-------------|--------
 __regex-expresion__| could be any valid [golang based regular expressions](https://github.com/google/re2/wiki/Syntax). These regular expressions should have defined [capturing groups](http://www.regular-expressions.info/refcapture.html)<br/> __Limitation__:  we can not use the "\|" character because of this character would alter the `${VAR\|SELECTOR\|TRANSFORMATOR}` struct.  ( In the future would should improve syntax to avoid this limitation).
 __substitution-expression__| any string containing valid [back references](http://www.regular-expressions.info/refcapture.html) ie "\1, \2, \3, ..... etc", for previously defined capturing groups
 

#### Defined transformations

* __STRING__ : return the selected value as an string
* __DEC2ASCII__: decode a dotted separated string with numbers as ascii bytes of a string ("100.101.102.97.117.108.116" will be "default")
* __MAC__: decode a string  as MAC address.( .1.3.6.1.2.1.10.127.1.3.3.1.2.89 = STRING: 60:2a:d0:40:42:1a ) will be decoded as MAC with this format `:${VAL1|DOT[0:]|MAC}` (>version 0.10)

#### Example

We can use inetCidrRouteIfIndex as example:
The following is the output from one row of the table with "snmpwalk".

````bash
IP-FORWARD-MIB::inetCidrRouteIfIndex.ipv4."0.0.0.0".0.2.0.0.ipv4."10.0.2.2" = INTEGER: 2
````

equivalent to that.(I've separated baseOID from Index)

````bash
BaseOID[1.3.6.1.2.1.4.24.7.1.7]+ INDEX[.1.4.0.0.0.0.0.2.0.0.1.4.10.0.2.2] = INTEGER: 2
````

when looking for index info here inetCidrRouteEntry (http://oid-info.com/get/1.3.6.1.2.1.4.24.7.1), we have found the following

```bash
INDEX {
inetCidrRouteDestType, ipv4(1), ipv6(2), ipv4z(3), ipv6z(4)) = 1
inetCidrRouteDest,	= 4 - 0.0.0.0
inetCidrRoutePfxLen,    = 0.2
inetCidrRoutePolicy,    = 0.0
inetCidrRouteNextHopType = 1
inetCidrRouteNextHop  = 4 -> 10.0.2.2
}

````bash
In this case we would like by example take RouteDest and RouteNextHop as IndexTAG with

```bash
${IDX1|DOT[2:5]|STRING} -> ${IDX1|DOT[12:15]|STRING}
````

this will equivalent to 

````bash
${IDX1|DOT[2:5]|} -> ${IDX1|DOT[12:15]|}
````

And the output result will be

````bash
0.0.0.0 -> 10.0.2.2
````

---


# Examples

## Example of Indexed with multiple indirect TAG

Lets assume that we want to retrieve the following values of the following metrics
The base OID from those are `1.1` and they have a double index, [X,Y] which gives us the following information:

```bash
X=related interface
Y=related object
```

```bash
myMetricTable: 1.1 [X,Y]
1.1[.1.1] | 51
1.1[.2.1] | 52
1.1[.3.1] | 53
1.1[.4.1] | 54
1.3[.1.1] | 21
1.3[.2.1] | 22
1.3[.3.1] | 23
1.3[.4.1] | 24
```

To identify those metrics, we need to retrieve the tag value on `myFinalRealTagTable - 1.5`, but we need a double index iteration to retrieve the desired tag and it is indexed by `M`.
The process would be:

```bash
1.2.[X.Y] --> [Z] --> 1.10.[X.Z] --> [M] --> 1.5.[M] --> tag
```

```bash
myIndexTagTable: .1.2[X.Y] | [Z]
.1.2.[1.1] | 2
.1.2.[2.1] | 2
.1.2.[3.1] | 2
.1.2.[4.1] | 2
---
myRealTagTable: .1.10.[X.Z] | [M]
.1.10.1.2 | 90
.1.10.2.2 | 91
.1.10.3.2 | 92
.1.10.4.2 | 93
---
myFinalRealTagTable: .1.5.[M] | [TagValue]
.1.5.94 | "eth1"
.1.5.95 | "eth2"
.1.5.96 | "eth3"
.1.5.97 | "eth4"
```

As we can see we need to go over 3 different tables and different indexes to retrieve values that can identify each value of pair [X.Y].
To achieve that, we create the following measuement:

As we need to match with the metrics, we need to go over `myIndexTagTable` to retrieve and store indexes `[X.Y]`, so we configure the `IndexOID` as `1.2`. The output of this table will be used as the next one:

```bash
Def:
IndexOID: "1.2" | [X.Y]
Result: "2, 2, 2, 2 | [Z]
```

To retrieve the second table, we cannot use the value given `Z` becouse we need also the var `X` in order to match the index. As the `X` is present on table `myIndexTagTable` and `myRealTagTable`, we can format the index to achieve the desired [X.Z]

```bash
Def:
TagOID: ".1.10",
IndexFormat: "${IDX1|DOT[0:0]|STRING}.$VAL1" --> [X.Z]
Result: "90, 91, 92, 93"
```

With the matched value of `M`, we can retrieve the final table, in this case, we don't need to setup a format as the required index is directly indexed by [M]

```bash
Def:
TagOID: ".1.5",
IndexFormat: ""
Result: "eth1, eth2, eth3, eth4"
```

On this point, we will have map that relates our initial [X.Y] to our desired tags
Remember that on multiple inderect measurement, the final index is equal than the IndexOID to be equal than the metrics

```bash
1.1 | eth1
1.2 | eth2
1.3 | eth3
1.4 | eth4
```

## Example of MultiIndex: ifMIB

All the metric of IfMIB are based on `IfIndex OID: 1.3.6.1.2.1.2.2.1.1`. According to its description:
> A unique value for each interface. Its value ranges between 1 and the value of ifNumber. The value for each interface must remain constant at least from one re-initialization of the entity's network management system to the next re-initialization.

As all related metrics shares the same index, we can define a simple `Direct Index` measurement to retrieve those metrics, but, for this case, metrics that are constants, like tags, doesn't need to be polled every frequency, it can be gathered on filter freq.

On this example, we will use the following metrics that are, in some way, static for each interface:

Name | OID
--- | ---
ifDesc | .1.3.6.1.2.1.2.2.1.2
ifName | .1.3.6.1.2.1.31.1.1.1.1
ifAlias | .1.3.6.1.2.1.31.1.1.1.18

The related fields that we want to gather are defined as SNMP Metrics:

ID | FieldName | BaseOID | DataSrcType
--- | --- | --- | ---
ifHCInOctets |ifHCInOctets |.1.3.6.1.2.1.31.1.1.1.6 |COUNTER64
ifHCOutOctets | ifHCOutOctets | .1.3.6.1.2.1.31.1.1.1.10 |COUNTER64

A simple walk over those metrics gives us the follow index/value:

```bash
ifHCInOctets >
.1.3.6.1.2.1.31.1.1.1.6[.1] | 3
.1.3.6.1.2.1.31.1.1.1.6[.2] | 4
.1.3.6.1.2.1.31.1.1.1.6[.3]| 20


ifHCOutOctets >
.1.3.6.1.2.1.31.1.1.1.6[.1] | 5
.1.3.6.1.2.1.31.1.1.1.6[.2] | 7
.1.3.6.1.2.1.31.1.1.1.6[.3]| 35
```

In order to identify those metrics with `ifAlias`, `ifName`, `ifDesc` tags, we create a create a `Multi Index Measurement`,with 3 different indexes. As the indexes **don't need to be ordered** we will define it in a non-logic order

Index | Type | Label | Index Description | Index Base OID | Index Tag | Index Tag Format | Index Dependency
--- | --- | --- | --- | --- | --- | --- | ---
0 | Indexed with direct Tag | ifName | Index to retrieve ifName tag | .1.3.6.1.2.1.31.1.1.1.1 | ifName | -- | ""
1 | Indexed with direct Tag | ifDesc | Index to retrieve ifDesc | .1.3.6.1.2.1.2.2.1.2 | ifDesc | -- | IDX{2};DOT[0:0];SKIP  (note that it references with an index that is greater than the current one. Remember that the order is not needed)
2 | Indexed with direct Tag | ifAlias | Index to retrieve ifAlias | .1.3.6.1.2.1.31.1.1.1.18 | ifAlias | -- | IDX{0};DOT[0:0];SKIP

As each index refers to a single measurement and it has its own lifecycle, the Dependency is used only to create the final result. In this case, we want to retrieve the final tags from IDX{1} (it will heridate the IDX{2} and IDX{0}) so:

```bash
Result: "IDX{1}"
```

Example of measurement definition:

![ifMIB_MultiIndex]

Each index will save on its own structure the gathered data based on its definition:

```bash
Index 0 | ifName >
.1.3.6.1.2.1.31.1.1.1.1[.1] | "Te1/0/1" || ifName
.1.3.6.1.2.1.31.1.1.1.1[.2] | "Te1/0/2" || ifName
.1.3.6.1.2.1.31.1.1.1.1[.3]| "Te1/0/3" || ifName

Index 1 | ifDesc >
.1.3.6.1.2.1.2.2.1.2[.1] |  "TenGigabitEthernet1/0/1" || ifDesc
.1.3.6.1.2.1.2.2.1.2[.2] |  "TenGigabitEthernet1/0/2" || ifDesc
.1.3.6.1.2.1.2.2.1.2[.3]|  "TenGigabitEthernet1/0/3" || ifDesc

Index 2 | ifAlias >
.1.3.6.1.2.1.31.1.1.1.18[.1] |  "To home" || ifAlias
.1.3.6.1.2.1.31.1.1.1.18[.2] |  "-- <null, not defined!>" || ifAlias
.1.3.6.1.2.1.31.1.1.1.18[.3]|  "To shop" || ifAlias

```

When building the result, all the measurements will be ordered based on dependencies, so, it will processed on the following order:

```bash
0| Index 0 | ifName  | Not dependency
1| Index 2 | ifAlias | Dependency on Index 0
2| Index 1 | ifDesc | Dependency on old Index 2
```

The building index schema follows as:

{{< postimage "images/schema_example_multiple_indexes_ifmib.jpg" "Example IfMib Multiple Indexes" >}}

When a dependency exist, it tries to heridate the tags that matches the current OID. If it doesn't match, it applies the strategy, in this case `SKIP`

```bash
Index 2 | ifAlias - IDX{0};DOT[0:0];SKIP >
.1.3.6.1.2.1.31.1.1.1.18[.1] | "Tel1/0/1|To home" || ifName, ifAlias 
.1.3.6.1.2.1.31.1.1.1.18[.3]| "Te1/0/3|To shop" || ifName, ifAlias

---
index [.2] it has been skipped due it doesn't exist on ifAlias and it will be skipped
```
The Index 2 dependency will use the new Index 1:

```bash
Index 1 | ifDesc - IDX{2};DOT[0:0];SKIP
.1.3.6.1.2.1.2.2.1.2[.1] |  "Tel1/0/1|To home|TenGigabitEthernet1/0/1"
.1.3.6.1.2.1.2.2.1.2[.3]|  "Te1/0/3|To shop|TenGigabitEthernet1/0/3"

---
index [.2] it has been skipped due it doesn't exist on processed Index 1 and it will be skipped
```

The result (`IDX{1}`) will be directly the Index 1- ifAlias with the new heridated tags:

```bash
.1.3.6.1.2.1.2.2.1.2[.1] |  "Tel1/0/1|To home|TenGigabitEthernet1/0/1" || ifName | ifAlias | ifDesc
.1.3.6.1.2.1.2.2.1.2[.3]|  "Te1/0/3|To shop|TenGigabitEthernet1/0/3"  || ifName | ifAlias | ifDesc
```

So, the final gathered metrics will be:

```bash
ifHCInOctets >
.1.3.6.1.2.1.31.1.1.1.6[.1] | 3   || ifName="Tel1/0/1",ifAlias="To home",ifDesc="TenGigabitEthernet1/0/1"
.1.3.6.1.2.1.31.1.1.1.6[.3]| 20  || ifName="Te1/0/3",ifAlias="To shop",ifDesc="TenGigabitEthernet1/0/3"

ifHCOutOctets >
.1.3.6.1.2.1.31.1.1.1.6[.1] | 5   || ifName="Tel1/0/1",ifAlias="To home",ifDesc="TenGigabitEthernet1/0/1"
.1.3.6.1.2.1.31.1.1.1.6[.3]| 35  || ifName="Te1/0/3",ifAlias="To shop",ifDesc="TenGigabitEthernet1/0/3"
```
