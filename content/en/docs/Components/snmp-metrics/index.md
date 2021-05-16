---
title: "SNMP Metrics"
description: "This component allows the user define core metrics to be collected from the device."
lead: "This component allows the user define core metrics to be collected from the device."
date: 2021-03-16T08:43:34+01:00
lastmod: 2021-03-16T08:43:34+01:00
draft: false
menu:
  docs:
    parent: "components"
weight: 140
toc: true
---

## View

Configured **SNMP Metrics** are shown as table. This will appear every time you navigate from menu.

{{< postimage "images/config_snmp_metrics_list.JPG" "SNMP Metric List" >}}

## Add/Edit SNMP Metrics

- **Add**: In the SNMP Metrics list click on new button ![New Button](images/new_button.JPG)

- **Edit**: Click on SNMP Metric edit's button ![Edit Button](images/edit_button.JPG)

The Create/Modify form will show up:

{{< postimage "images/config_snmp_metrics_edit.jpg" "Create/Modify SNMP Metrics" >}}

### Configuration Parameters

Type of settings|Config Parameter| Description
----------------|----------------|----------------
Core settings|ID| Text String that uniquely identify the metric recommended use the MIB Name, should be unique in the database
||FieldName | Set the "field" name inside a Influx measurement, could be the same of id or not, should be unique inside the measurement where the metric will be sent.
Metric settings|DataSrcType|Origin data type could be one of these:<br/><ul><li>(SNMP SMI Type) INTEGER</li><li>(SNMP SMI Type) Integer32 </li><li>(SNMP SMI Type) Gauge32</li><li>(SNMP SMI Type) UInteger32</li><li>(SNMP SMI Type) Unsigned32</li><li>(SNMP SMI Type) Counter32 </li><li>(SNMP SMI Type) Counter64 </li><li>(SNMP SMI Type) TimeTicks</li><li>(SNMP SMI Type) OCTETSTRING</li><li>(SNMP SMI Type) IpAddress</li><li>(Cooked type) TIMETICKS [Compute TimeTicks to seconds]</li><li>(SNMP SMI Type) BIT STRING (needs a named-number enumeration in extradata) </li><li>(Cooked type) COUNTER32 [Compute increments for 32 bit counters]</li><li>(Cooked type) COUNTER64 [Compute increments for 64 bit counters]</li><li>(Cooked type) COUNTERXX [Compute increments only if it really increments -- as NON_NEGATIVE_DERIVATIVE does--]</li><li>(Cooked type) HWADDR [ Translate Hardware Address (MAC) from STRING]</li><li>(Cooked type) STRINGPARSER [ Compute values from Regex ]</li><li>(Cooked type) STRINGEVAL [evaluate data from other metrics]</li><li>(Cooked type) CONDITIONEVAL [evaluate OID table with boolean conditions and get number of true values]</li><li>(Cooked type) BIT STRING CHECK [needs the number bit to check in extradata , returns 1 or 0]</li><li>(Cooked type) ENUM - [needs named-number enumeration on extradata]</li><li>(Cooked type) MULTI-STRINGPARSER  [generates multiple fields or tags from an snmp returned string and an Regular expression with multiple capturing groups]</li></ul>
||IsTag|If true data will be sent as a Influxdb "TAG". (default is false)
||BaseOID|Snmp OID for this metric ( could be the base OID when it belongs to a snmp indexed table), could not be set only if datasrctype is of type "String Eval".
||ExtraData| data needed to special data types as String Eval(evaluated expression needed), String Parser(regex needed) or OctetString to add a trim function (0.8.2+)
||GetRate| Only useful on  COUNTER64/32/XX types , if true value sent will be the computed difference and after divided by the number of elapsed seconds between snmp calls
||Conversion|before 0.8.0 version all data was sent as FLOAT64 numbers, from new 0.8.0 version could select output format depending on the DataSrcType, usually FLOAT ( float64 ) , INTEGER (int64) , BOOLEAN or STRING. Not all conversions all available on all input datasources. Unsigned Integer conversion not supported yet.
Metric autoscale|Scale| if set (as a floating point value) computed data will be scaled before sent.
||Shift| if set (as a floating point value) computed data will be shifted before sent.
Extra settings|Description | any useful discretion to understand data gathered for this metric ( recommended place here units of the measurement).

_**Notes**_:
The SnmpCollector Metric engine computes all its data and sent them as floating point values to the database, except for tags that will be sent as text strings

### Multi edit in SNMP Metrics 

Multi edit has 2 options: Remove and Change property

1. Remove: Allows to remove all the selected items

2. Change property: Allows to change the selected property of all the selected items. The properties are the same defined in the configuration.

Property| Type| Options/Values
----------------|----------------|----------------
DataSrcType|Selector|<li>Int,Int32,Gauge32,UInt32,Unsigned32,Counter32,Counter64</li><li>TimeTicks, OCTETSTRING, IpAddress</li><li>COUNTER32, COUNTER64, COUNTERXX</li><li>HWADDR, STRINGPARSER,STRINGEVAL, CONDITIONEVAL</li></ul>
Scale| Input| Any number
Shift| Input| Any number
IsTag| Selector | True / False 
|<img width=50/>|<img width=50/>|<img width=50/>|

## About OCTETSTRING/Hex-String

From 0.8.0 version, you could use OCTETSTRING as datasource Types for Integers codified as Hex-String, in this case you should enable conversion to INTEGER.

 > If updated from release < 0.8.0 to  release >= 0.8.0 version you should do a manual upgrade of your database `update snmp_metric_cfg set Conversion=3 where datasrctype='OCTETSTRING'; if not done any OCTETSTRING data won't be collected.

From 0.8.2 version, it is possible to apply trim functions on octet-string metric based on `ExtraData`

Available options:

- trimright('xyz'): removes trailing characters defined by 'xyz'
- trimleft('xyz'): removes leading characters defined by 'xyz'
- trim('xyz'): removes trailing and leading characters defined by 'xyz'
- trimspace : remove all leading and trailing tabs, whitespaces, newlines and return carriage this is (\t \n\r)

Example:

```
OID > .1.2.3.4.5

.1.2.3.4.5.1 = "   Test string with blank spaces   "

ExtraData: trimspace

Output:
.1.2.3.4.5.1 = "Test string with blank spaces"
```


## About BIT STRING and BIT STRING CHECK Metric Type

From 0.7.2 version , there is the way to get data from BIT STRING OID's.  BITS is a pseudo data type that specifies a collection of labelled bits as explained here !(https://www.webnms.com/snmp/help/snmpapi/snmpv3/using_mibs_in_applns/bits_datatype.html)

If you want config a BIT STRING you should also provide the bit->label comma separated array, in the ExtraData Field.

By example:`sunday(0), monday(1), tuesday(2), wednesday(3), thursday(4), friday(5), saturday(6)`

if data got on the snmpquery is this bitstring , The output will be an concatenated labeled array sent as and string to the influxDB.
Examples:

Snmp query Out |  value sent 
---------------|---------------
'011001B' | "monday,tuesday,saturday"
'100001B'| "sunday,saturday"
'010000B'| "monday"
'01B' | "monday"

If we would like to know only if certain bit has been check  we can use the Cooked Type BIT STRING CHECK "BITSCHECK". Suppose you can get with a BIT STRING the state of one device and the bit,label map is

bit number|label
------|-----
0|up
1|down
2|restoring
3|failed

You can check if the device is ok by only check that the byte "0" is set ( the device will be up) , in this case the metric will send 1  floating point number if the selected byte is set or 0 if not.

## About ENUM Metric Type

From 0.7.7 version , there is the way to map enum 
 values and send it to InfluxDB as STRING (as Tag or Field). The enum-named values can be defined on the ExtraData field with the following syntax:
`<CUSTOM_DESCRIPTION>(<ENUM>)`

If a returned value by the OID query is not mapped on the extradata, the value will be sent directly as a STRING.

By example, the following OID query `ifType OID - 1.3.6.1.2.1.2.2.1.3` would return the type of interfaces. The ENUM metric Type would allow the user to map those results and add custom text to clarify its meaning:

With the following Metric:
```
SNMP possible results: 6,24,135

Base OID : 1.3.6.1.2.1.2.2.1.3
ExtraData: EthernetLike interfaces (6), SubInterfaces L2VLAN (135)
```
Would generate the following output to InfluxDB:

Snmp query Out | ExtraData | value  sent
---------------| --------- | ------------
6 | EthernetLike interfaces (6) | "EthernetLike interfaces (6)"
24 | (non mapped) | "24"
135 | SubInterfaces L2VLAN (135) | "SubInterfaces L2VLAN (135)"

## About MULTISTRINGPARSER Metrics

From 0.7.6 version , there is  a new metric type and the first capable to generate more than one field/tag with only one metric definition.
Will parse data with regex with capturing groups and each capture group will be attached to a definition  ITEM, each definition ITEM will generate a Field or a Tag. We assume that the origin got data from devices are readable strings.

Configuration will be placed in:

- __FieldName:__ Comma separated value of ITEM definitions
- __ExtraData:__ Regular expression with multiple capturing groups 

by example:

- __FieldName:__ `F|field_name_1|CONV1,F|field_name_2|CONV2,T|tag_name1|CONV3`
- __ExtraData:__ `'([^;]*);([^;]*);([^;]*);' <-regexp`

Is necessary configure as many ITEMS as capturing groups.

Each ITEM  in  the FieldName needs for 3 parameters before to be sent.

`TYPE|NAME|CONVERSION`

configuration| what means
-------------|------------
TYPE| F = Field , T = Tag ( this parameter invalidates the use of isTag flag in the metric definition)
NAME|Field/Tag Name will be sent to the InfluxDB
CONVERSION| how should evaluate the returned string? possible values (STR,INT,FP,BL)

Fields/Tag definitions will be evaluated as:

CONVERSORS |  Conversion type (from String)
--------|---------------------------------------
STR | to maintain as string ( no conversion will be done)
INT| to convert as and 64bits integer
FP| to convert as and 64 bits floating point
BL| to convert as a boolean


## About STRINGEVAL Metrics

The StringEval Metric type let us compute any value from an "expression evaluation" , the expression could be mathematical or logical. Available documentation  on what kind of evaluations could snmpcollector do could be read here [EXPRESSION TUTORIAL](https://github.com/Knetic/govaluate/blob/master/MANUAL.md)

**Important defining `FieldNames` that contains characters like [0-9], [-], or something else, needs to be escaped using brackets on the formula. Example: [1m_ifHCInOctets] * [ifHighSpeed]**

### Usable Variables

What variables could I use on  my expressions?, Here the variables 

Variable| Description
----------------|----------------
FieldNames| Any of the fieldNames placed on the same measurement
NF| especial variable that could be used to get the "number of fields" in the measurement (as in AWK)
NR| especial variable that could be used to get the "number of rows" in the measurement (after all filters has been applied), on scalar/value measurements, NR will be always 1 . On tabular/Indexed measurements NR will be the current number of rows (after filters applied) 
NFR(>0.7.6)| especial variable  that could be used to get the"non filtered rows", that counts the "number of rows" as the below NR but before all filters has been applied.
Catalog Vars( > 0.7.5)| Any of the defined variables in the catalog

### About Check 

When defining a new STRINGEVAL metric, the formula in extra data is stored on the configuration database but not evaluated yet since it needs to be placed into a measurement to check if expression its ok (validating all variables used in the expression are in the before table

## Examples

### A.Creating new StringEval Metric


Let's create a SNMP Metric that computes the `TotalOctets` on each port. The formula follows as:
````
TotalOctets = (InBytes + OutBytes)
````

#### A.1 Add needed metrics to get our new string eval metric:

ID|FieldName|BaseOID|DataSrcType|ExtraData|GetRate|Scale|Shift|IsTag
--|---------|-------|-----------|---------|-------|-----|-----|-----
ifHCInOctets|InBytes|.1.3.6.1.2.1.31.1.1.1.6|COUNTER64| |false|0|0|false
ifHCOutOctets|OutBytes|.1.3.6.1.2.1.31.1.1.1.10|COUNTER64| |false|0|0|false
|<img width=25/>|<img width=50/>|<img width=50/>|<img width=50/>|<img width=50/>|<img width=50/>|

#### A.2 Add `TotalOctets` metric:

ID|FieldName|BaseOID|DataSrcType|ExtraData|GetRate|Scale|Shift|IsTag
--|---------|-------|-----------|---------|-------|-----|-----|-----
TotalOctets|TotalOctets| |STRINGEVAL|ifHCInOctets + ifHCOutOctets| |0|0|false


#### A.3 Result

The SNMP Metric list must be like:

ID|FieldName|BaseOID|DataSrcType|ExtraData|GetRate|Scale|Shift|IsTag
--|---------|-------|-----------|---------|-------|-----|-----|-----
ifHCInOctets|InBytes|.1.3.6.1.2.1.31.1.1.1.6|COUNTER64| |false|0|0|false
ifHCOutOctets|OutBytes|.1.3.6.1.2.1.31.1.1.1.10|COUNTER64| |false|0|0|false
TotalOctets|TotalOctets| |STRINGEVAL|InBytes + OutBytes| |0|0|false

### B. Creating new StringParser Metric

Some OIDs results are full strings that contains on some part of its texts the value that we wants to capture.
In this example we will work with an Infoblox device and the following OID that gives us Fan1 RPM: `.1.3.6.1.4.1.7779.3.1.1.2.1.10.1.3.28`, with an output as: `FAN 1: 8725 RPM`


#### B.1 Define the regex that would capture the group:

We need to capture the RPM speed, so we define the capture group as: `.*: *([0-9]*)* *RPM`

#### B.2 Add new StringParser metric

ID|FieldName|BaseOID|DataSrcType|ExtraData|GetRate|Scale|Shift|IsTag
--|---------|-------|-----------|---------|-------|-----|-----|-----
Infoblox_FanState1|fanState1| |STRINGPARSER| `.*: *([0-9]*)* *RPM` | |0|0|false
