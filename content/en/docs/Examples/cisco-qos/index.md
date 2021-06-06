---
title: "Retrieve metrics from Cisco QoS - CISCO-CLASS-BASED-QOS-MIB"
description: ""
lead: ""
date: 2021-03-16T08:43:34+01:00
lastmod: 2021-03-16T08:43:34+01:00
draft: false
menu:
  docs:
    parent: "Examples"
weight: 61
toc: true
---


<!-- Images Rel -->
[mi-indexed]: /webUI/Examples/QoS/multiindex_indexed.jpg "(snmp Table) Indexed with direct TAG"
[mi-indexed_it]: /webUI/Examples/QoS/multiindex_indexed_it.jpg "(snmp Table) Indexed with indirect TAG"
[mi-indexed_mit]: /webUI/Examples/QoS/multiindex_indexed_mit.jpg "(snmp Table) Indexed with multiple indirect TAG"

<!-- CM -->
[cbqosifindex_meas]:  https://github.com/toni-moreno/snmpcollector/blob//gh-pages/images/webUI/Examples/QoS/cbqosifindex_meas.jpg "example of index dfefinition of cbqosifIndex"
[cbqospolicydirection_meas]: /webUI/Examples/QoS/cbqospolicydirection_meas.jpg "example of definition of cbqospolicydirection"
[cbqoscmname_meas]: /webUI/Examples/QoS/cbqoscmname_meas.jpg "example of definition of cbQosCMName index"
[cbqospolicymapname_meas]: /webUI/Examples/QoS/cbqospolicymapname_meas.jpg "example of definition of cbQosPolicyMapName"
[cbqospolicymapname_meas_index]: /webUI/Examples/QoS/cbqospolicymapname_meas_index.jpg "example of definition of cbQosPolicyMapName multi TagOID"
[cbqoscminfo_meas]: /webUI/Examples/QoS/cbqoscminfo_meas.jpg "example of definition of cbQosCMInfo"
[runtime_qoscm]: /webUI/Examples/QoS/runtime_qoscm.jpg "Example of QoS CMStats"

<!--TS -->
[cbqoscmname_ts_meas]: /webUI/Examples/QoS/cbqoscmname_ts_meas.jpg "Example of CMName from TSStats"
[cbqoscmname_meas_index]: /webUI/Examples/QoS/cbqoscmname_meas_index.jpg "example of definition of cbqosCMName multi TagOID"
[cbqospolicyname_ts_meas]: /webUI/Examples/QoS/cbqospolicyname_ts_meas.jpg "Example of PolicyMapName from TSStats"
[cbqoscmname_meas_index_ts]: /webUI/Examples/QoS/cbqoscmname_meas_index_ts.jpg "example of definition of cbqosPolicyMap multi TagOID"
[runtime_qosts]: /webUI/Examples/QoS/runtime_qosts.jpg "Example of QoS TSStats"
<!-- END -->


# Example: Retrieve metrics from Cisco QoS - CISCO-CLASS-BASED-QOS-MIB

This example will show up how to retrieve QoS metrics from a Cisco Device, based on one of the most complex MIB - `CISCO-CLASS-BASED-QOS-MIB`
**Only valid on 0.9**

---

## Table of contents

- 0 Prerequisites : What and from we are going to collect our data?
  - 0.1 SNMP Device model
  - 0.2 Select metrics to collect data
    - 0.2.1 Available object tables
    - 0.2.2 Objects identifiers
    - 0.2.3 Tags - Indexes definition
  - 0.3 Measurements
- 1 CMStats
  - 1.1 Configuring metrics
  - 1.2 Configuring measurements
    - 1.2.1 Overview
    - 1.2.2 Index 0 | Service Policy Index Tag: cbQosIfIndex
    - 1.2.3 Index 1 | Service Policy Index Tag: cbQosPolicyDirection
    - 1.2.4 Index 2 | ClassMap Tag: cbQosCMName
    - 1.2.5 Index 3 | PolicyName Tag: cbQosPolicyMapName
    - 1.2.6 Index 4 | ClassMap Tag: cbQosCMInfo
    - 1.2.7 Result
  - 1.3 Configuring filters
    - 1.3.1 OID Condition - Filter classmap
    - 1.3.2 MeasurementFilter - Filter classmap
  - 1.4 Configuring Measurement Group
  - 1.5 Configuring Device
  - 1.6 Reload configuration
  - 1.7 Check your results!
- 2 TSStats
  - 2.1 Configuring metrics
  - 1.2 Configuring measurements
    - 1.2.1 Overview
    - 2.2.2 Index 0 | Service Policy Index Tag: cbQosIfIndex
    - 2.2.3 Index 1 | Service Policy Index Tag: cbQosPolicyDirection
    - 2.2.4 Index 2 | ClassMap Tag: cbQosCMName
    - 2.2.4 Index 3 | PolicyMap Tag: policyMapName
    - 2.2.4 Result
  - 2.3 Configuring filters
    - 2.3.1 OID Condition - Filter TS
    - 2.3.2 MeasurementFilter - Filter classmap
  - 2.4 Configuring Measurement Group
  - 2.5 Configuring Device
  - 2.6 Reload configuration
  - 2.7 Check your results!

---

## 0. Prerequisites : What and from we are going to collect our data?

### 0.1 SNMP Device model
In order to select the correct metrics for our device, we must check first what model it is.
If you don't know the model, you can pre-add the device on SNMP Device Component and do a test connection. To do that:
- Click on `SNMP Device` menu item
- A table with configured devices will show up. Press "New" button or edit an existing one to access to the configuration form.
- Fill the device with the basic connection information
- If everything is OK, you will be able to do a "Test Connection". This button will show up a console with System information  of the Device. 

### 0.2 Select metrics to collect data

To define the metrics to retrieve data from we need first to analyze how CISCO-CLASS-BASED-QOS-MIB works
I will use the example exposed on: https://blog.pierky.com/cisco-class-based-qos-snmp-mib-and-statistics-monitor-for-nms/ as it will help to underestand a real case

#### 0.2.1 Available object tables


In `CISCO-CLASS-BASED-QOS-MIB` the tables are defined by each kind of object that is available to be configured on QoS.
Each object has a **CFG Table** that shows config information and **StatsTable** with the available metrics to retrieve data from.

On this example, we will work over the following objects and tables:

Object | CFG Table | Stats Table
--- | --- | ---
Policy Map | `cbQosPolicyMapCfg - 1.3.6.1.4.1.9.9.166.1.6.1.1` | --
ClassMap | `cbQosClassMapCfg - 1.3.6.1.4.1.9.9.166.1.7.1.1` | `cbQosCMStatsTable - 1.3.6.1.4.1.9.9.166.1.15.1.1`
MatchStmt | `cbQosMatchStmtCfg - 1.3.6.1.4.1.9.9.166.1.8.1.1` | `cbQosMatchStmtStats -  1.3.6.1.4.1.9.9.166.1.16.1.1`
TS | `cbQosTSCfg - 1.3.6.1.4.1.9.9.166.1.13.1.1` | `cbQosTSStats - 1.3.6.1.4.1.9.9.166.1.19`

<p align="center">
   :information_source: QoS objets table
</p>

As PolicyMap doesn't have stats on itself, we will cover in this example how to retrieve data from:

- ClassMap Object
- TS Object

#### 0.2.2 Objects identifiers

Once we have decided objects to retrieve data from, we need to check how to identify each metrics and what tags do we need

The tree hierarchy of QoS follows as:

```
Interface 
| - Direction
| - PolicyMap
    |- ClassMap
      |- MatchStmt
      |- TS
```

So, the deeper object is, more tags we will need to retrieve in order to be able to identify it:

Object | Tags needed
--- | ---
PolicyMap | *Interface, Direction, PolicyMap*
ClassMap | *Interface, Direction, PolicyMap, ClassMap*
MatchStmt | *Interface, Direction, PolicyMap, ClassMap, MatchStmt*
TS | *Interface, Direction, PolicyMap, ClassMap, MatchStmt*

<p align="center">
   :information_source: QoS objets table-tags relation
</p>

#### 0.2.3 Tags - Indexes definition

Each object defined on XXXXStatsTable and XXXXCfg tables have a double index.
In order to reference them to the document, lets identify and name them:

Base | Index | Name
--- | --- | ---
**XXX**.YYY | XXX | Service Policy Index
XXX.**YYY** | YYY | Object Index

<p align="center">
   :information_source: Index definition
</p>

### 0.3 Measurements

As we have seen, each object needs specific tags to identify those metrics and, based on that they are double indexed, we will create a single measurement to retrieve each object stats

# 1. CMStats

The first set of data that we want to retrieve are the CMStats

## 1.1 Configuring metrics

Available metrics are defined on `cbQosCMStatsTable - 1.3.6.1.4.1.9.9.166.1.15.1.1`.
In our example we will configure the following metrics:

ID | Field Name | IsTag | DataSrcType | GetRate | Base OID/Extradata | Scale | Shift |
---|----------- | ----------- | ----- | ------------------ | ------- | ----- | ----- |
cisco_cbQosCMPrePolicyPkt64 | cbQosCMPrePolicyPkt64	| false | COUNTER64 | false |.1.3.6.1.4.1.9.9.166.1.15.1.1.3 | 0 | 0
cisco_cbQosCMPrePolicyByte64 | cbQosCMPrePolicyByte64 | false| COUNTER64 | false | .1.3.6.1.4.1.9.9.166.1.15.1.1.6 | 0 | 0
cisco_cbQosCMPrePolicyBitRate | cbQosCMPrePolicyBitRate | false | Gauge32 | false | .1.3.6.1.4.1.9.9.166.1.15.1.1.7 | 0 | 0

<p align="center">
   :information_source: QoS CMStats metrics configuration
</p>

As we have said, each metric will have a double index: `[Service Policy Index].[Object Index]`

## 1.2 Configuring measurements

Let's start to define the measurement

### 1.2.1 Overview

As we have defined, the CMStats needs the following tags to identify each metric:

- Interface
- Direction
- PolicyMap Name
- ClassMap Name
- [Extra] ClassMap Info

The main problem here is that each tag is being retrieved with **different indexes and different indexes dimension**, so in order to correlate those tags and indexes we need to define a **Multi Index Measurement**

To do that, go to `Influx Measurements` section and click on `+New`

Fill the `ID` and `Name` fields with:

Field | Value | Description
--- | --- | ---
ID | cisco_qos_cbQosCMStats | Unique SNMPCollector internal identifier of measurement
Name | cbQosCMStats | Measurement name that will be used on InfluxDB


<p align="center">
   :information_source: Measurement config section
</p>

On the `GetMode` field select:

Field | Value | Description
--- | --- | ---
GetMode | (snmp Table) Multiple indexes | --

<p align="center">
   :information_source: Measurement get mode
</p>

### 1.2.2 Index 0 | Service Policy Index Tag: cbQosIfIndex 

The first that we need to retrieve are interface related tags, name and direction.
According to documentation, we need to poll the following OIDs to retrieve those tags:

Name | OID | Index
--- | --- | ---
cbQosIfIndex |  1.3.6.1.4.1.9.9.166.1.1.1.1.4 | Service Policy

<p align="center">
   :information_source: Service policy index
</p>


A simple walk on this OID gives as value the `ifIndex`

```bash
> cbQosIfIndex: 1.3.6.1.4.1.9.9.166.1.1.1.1.4

1.3.6.1.4.1.9.9.166.1.1.1.1.4[.1043] = INTEGER: 1
1.3.6.1.4.1.9.9.166.1.1.1.1.4[.1099] = INTEGER: 1
```

Note that the `[.1043]` and `[.1099]` will be used on the following indexes definition as `Service Policy Index`

The value of `1` is the ifIndex index, so, if we retrieve data from ifName using that index we will be able to retrieve the related ifName of the interface:

```bash
> ifName: 1.3.6.1.2.1.31.1.1.1.1

1.3.6.1.2.1.31.1.1.1.1[.1] = "Te1/0/3"
```

As we have an indirect tag, we will create an index with **"(snmp Table) Indexed with indirect Tag"**

![mi-indexed_it]

And will fill with the following fields:

Field | Value | Description
--- | --- | ---
Index Label | cbQosIfIndex | Unique SNMPCollector internal identifier of measurement
Index Description | Index to retrieve interface name from cbQosIfIndex | --
Index Base OID | .1.3.6.1.4.1.9.9.166.1.1.1.1.4 | Base OID is the cbQosIfIndex which gives us the value of the ifMIB index
Index Tag OID | .1.3.6.1.2.1.31.1.1.1.1 | ifName TagOID that will match with cbQosIfIndex result
Index Tag | ifName | We will use directly the name of the field that is being retrieved, but you can set whatever you want as your Tag Key
Index Tag Format | -- | We don't need to format the ifName tag, but you are free to set up whatever you want!
Index Dependency | -- | We don't need a dependency as it will be the first OID to retrieve tag from

<p align="center">
   :information_source: Index 0 - cbQosIfIndex
</p>


![cbqosifindex_meas]

The final output map of pairs [index]:[tag] will be:

```bash
> Index: IDX{0} | cbQosIfIndex

[.1043] = "Te1/0/3" || ifName
[.1099] = "Te1/0/3" || ifName
```


### 1.2.3 Index 1 | Service Policy Index Tag: cbQosPolicyDirection 

Once we have the inferface name, we can get the direction of the applied policy on each interface, in order to retrieve that we can query the following OID that gives us the direction.
Note that the index keeps only in 1 dimension and it is related with the `Service Policy Index`

Name | OID | Index
--- | --- | ---
cbQosPolicyDirection | .1.3.6.1.4.1.9.9.166.1.1.1.1.3 | **Service Policy Index**

A simple walk on this OID gives the direction of the policy applied on an interface

```bash
> cbQosPolicyDirection:  

.1.3.6.1.4.1.9.9.166.1.1.1.1.3[.1043] = INTEGER: output(2)
.1.3.6.1.4.1.9.9.166.1.1.1.1.3[.1099] = INTEGER: input(1)
```

In this case, we don't need to do an indirection, as it is giving directly the value of the tag, so we can use a **"(snmp Table) Index with direct TAG"**

![mi-indexed]

As we can see, the indexes are also [.1043] and [.1099] - `Service Policy Index` and they are shared with the `IDX{0}`, the `cbQosIfIndex` index

We will fill the index with the following field/values:

Field | Value | Description
--- | --- | ---
Index Label | cbQosPolicyDirection | Unique SNMPCollector internal identifier of measurement
Index Description | Index to retrieve interface name from cbQosPolicyDirection | --
Index Base OID | .1.3.6.1.4.1.9.9.166.1.1.1.1.3 | Base OID is the cbQosPolicyDirection which gives us the value of the direction
Index Tag | policyDirection | We will use directly the name of the field that is being retrieved, but you can set whatever you want as your Tag Key
Index Tag Format | -- | We don't need to format the policyDirection tag, but you are free to set up whatever you want!
Index Dependency | IDX{0};DOT[0:0];SKIP | (*)

<p align="center">
   :information_source: Index 1 - cbQosPolicyDirection
</p>


(*) Dependency:

We set up a dependency with the IDX{0}, as they shares the same Index in order to heridate ifIndex Tags. We will use a SKIP strategy

![cbqospolicydirection_meas]

At this point, we will have that the `IDX{1}` will be:

```
> Index: IDX{0} | cbQosIfIndex

[.1043] = "Te1/0/3" || ifName
[.1099] = "Te1/0/3" || ifName

> Index: IDX{1} | cbQosPolicyDirection | IDX{0};DOT[0:0];SKIP

[.1043] = "Te1/0/3|2" || ifName | policyDirection
[.1099] = "Te1/0/3|1" || ifName | policyDirection
```

We just finished to map the first index `Service Policy Index` and feed it with `ifName` and `policyDirection` tags


### 1.2.4 Index 2 | ClassMap Tag: cbQosCMName

On this point, we are going to retrieve the ClassMap name. Despite of the above indexes, the table to retrieve the class map name has now a **double index**: `Service Policy`.`Object Index`

To retrieve values from CFG tables, the MIB provides an OID to get the index reference to the object, which it has to be retrieved from `cbQosConfigIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.2`

A walk over the cbQosConfigIndex:

```
> cbQosConfigIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.2

.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1043] = Gauge32: 1035
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1045] = Gauge32: 1029
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1047] = Gauge32: 1033
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1049] = Gauge32: 1037
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1051] = Gauge32: 1025
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1053] = Gauge32: 1027
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1085] = Gauge32: 1079
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1099] = Gauge32: 1063
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1101] = Gauge32: 1057
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1103] = Gauge32: 1061
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1105] = Gauge32: 1065
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1107] = Gauge32: 1025
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1109] = Gauge32: 1027
```

The name of the classmaps comes from the table: `cbQosCMName - .1.3.6.1.4.1.9.9.166.1.7.1.1.1`

A walk over the `cbQosCMName` table:

```
> cbQosCMName - .1.3.6.1.4.1.9.9.166.1.7.1.1.1

.1.3.6.1.4.1.9.9.166.1.7.1.1.1[.1025] = STRING: class-default
.1.3.6.1.4.1.9.9.166.1.7.1.1.1[.1029] = STRING: ICMP
.1.3.6.1.4.1.9.9.166.1.7.1.1.1[.1057] = STRING: NonLocal
```

Note that the index is directly the one recieved from cbQosConfigIndex:

```
> cbQosConfigIndex (filtered) - .1.3.6.1.4.1.9.9.166.1.5.1.1.2

...
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1045] = Gauge32: 1029
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1051] = Gauge32: 1025
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1101] = Gauge32: 1057
...
```

As we have said, the first index references to the `Service Policy Index` and the second index refers directly to the `Object Index`.


As we have an indirect tag, we will create an index with **"(snmp Table) Indexed with indirect Tag"**

![mi-indexed_it]

We will fill with the following field/values:

Field | Value | Description
--- | --- | ---
Index Label | cbQosCMName | Unique SNMPCollector internal identifier of measurement
Index Description | Index to retrieve CMName| --
Index Base OID | .1.3.6.1.4.1.9.9.166.1.5.1.1.2 | Base OID is the `cbQosConfigIndex` which gives us the value of the config related with cbQosCMName table
Index Tag OID | .1.3.6.1.4.1.9.9.166.1.7.1.1.1 | `cbQosCMName` TagOID that will match with `cbQosConfigIndex` result
Index Tag | cmName | We will use directly the name of the field that is being retrieved, but you can set whatever you want as your Tag Key
Index Tag Format | -- | We don't need to format the policyDirection tag, but you are free to set up whatever you want!
Index Dependency | IDX{1};DOT[0:0];SKIP | (*)

<p align="center">
   :information_source: Index 2 - cbQosCMName
</p>

(*) Dependency

The dependency in ths case is based on `IDX{1}` - cbQosPolicyDirection that already have ifName tag.
In this case, the metrics have indexes with dimension 2, so we need to change the check condition based only on the first index, the `Service Policy Index`, using the statement `DOT[0:0]`

![cbqoscmname_meas]

At this point, we will have that the `IDX{1}` will be:

```

> IDX{1}:

[.1043] = "Te1/0/3|2" || ifName | policyDirection
[.1099] = "Te1/0/3|1" || ifName | policyDirection

> Index: IDX{2} | cbQosCMName | IDX{1};DOT[0:0];SKIP

>> DOT[0:0] = [.1043] / [.1099]

[.1043][.1045] = "Te1/0/3|2|ICMP"           || ifName | policyDirection | cmName
[.1043][.1051] = "Te1/0/3|2|class-default"  || ifName | policyDirection | cmName
[.1099][.1101] = "Te1/0/3|1|NonLocal"       || ifName | policyDirection | cmName

```

We just started to map the both index `Service Policy`.`Object Index`


### 1.2.5 Index 3 | PolicyName Tag: cbQosPolicyMapName

As each class is attached to a policyName, we need to identify the classmap with the name of the policy.
The name of the policy can be retrieved from the following OID:

Name | OID | Index
--- | --- | ---
cbQosPolicyMapName | 1.3.6.1.4.1.9.9.166.1.6.1.1.1| **Service Policy Index**.**Object Index**

A simple walk over the `cbQosPolicyMapName`:

```
> cbQosPolicyMapName - 1.3.6.1.4.1.9.9.166.1.6.1.1.1

1.3.6.1.4.1.9.9.166.1.6.1.1.1[.1035] = STRING: LAN_Out
1.3.6.1.4.1.9.9.166.1.6.1.1.1[.1063] = STRING: CPP
```

The problem here, is that the pair index of `Service Policy Index.Object Index` is **different from the classmap object type**. In order to retrieve this informaton and keep the classmap index, we must query the following OID - `cbQosParentObjectsIndex` - `1.3.6.1.4.1.9.9.166.1.5.1.1.4` that gives us the relation between objects on QoS:

A simple walk over the `cbQosParentObjectsIndex`:

```
> cbQosParentObjectsIndex - 1.3.6.1.4.1.9.9.166.1.5.1.1.4

1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1043] = Gauge32: 0
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1045] = Gauge32: 1043
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1047] = Gauge32: 1045
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1049] = Gauge32: 1045
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1051] = Gauge32: 1043
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1053] = Gauge32: 1051
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1085] = Gauge32: 1051
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1099] = Gauge32: 0
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1101] = Gauge32: 1099
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1103] = Gauge32: 1101
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1105] = Gauge32: 1101
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1107] = Gauge32: 1099
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1109] = Gauge32: 1107

```

The object relates the child with the parent, so filtering the, the pair of OID from classmap we will obtain the index of the policy attached to:

```
...
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1045] = Gauge32: 1043
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1051] = Gauge32: 1043
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1101] = Gauge32: 1099
...
```

The index values referes to the `Object Index` from `PolicyMap`, so the new indexes that we want to build in order to query the PolicyName, must be:

```
[.1043][.1043] - PolicyMap
[.1099][.1099] - PolicyMap
```

With the information above we need to define a new measurement, but in this case, we need to do at least 2 indirects and, in the middle modify the check condition with another indexes, so we define the measurement as **"(snmp Table) Indexed with multiple indirect TAG"**

![mi-indexed_mit]

We fill the index with the following field/values:

Field | Value | Description
--- | --- | ---
Index Label | cbQosPolicyMapName | Unique SNMPCollector internal identifier of measurement
Index Description | Index to retrieve cbQosPolicyMapName| --
Index Base OID | .1.3.6.1.4.1.9.9.166.1.5.1.1.4 | Base OID is the `cbQosParentObjectsIndex` which gives us the value of the config related with `Object Index` from `PolicyMap`
Index Tag | policyMapName | We will use directly the name of the field that is being retrieved, but you can set whatever you want as your Tag Key
Index Tag Format | -- | We don't need to format the policyDirection tag, but you are free to set up whatever you want!
Multi Tag OID | (*) | Multiple indirects to obtain the PolicyName and keep it on ClassMap pair of `Service Policy Index`.`Object Index`
Index Dependency | IDX{2};DOT[0:1];SKIP | (**)

<p align="center">
   :information_source: Index 3 - cbQosPolicyMapName
</p>

![cbqospolicymapname_meas]


**(*) Multi Tag OID**

We will define 2 TagOID:

TagOID 0:

Field | Value | Description
--- | --- | ---
Index TagOID | .1.3.6.1.4.1.9.9.166.1.5.1.1.2 | Index TagOID to retrieve the `Object Index` from `PolicyMap`
IndexFormat | `${IDX1\|DOT[0:0]\|STRING}.$VAL1`

This IndexFormat will modify the check condition and will create the desired index in order keep iterating over indirect tables. Following with the example:


The generated index is just the `Service Policy Index`.`Object Index` from `PolicyMap`, so we can use this to retrieve any parameter from `PolicyMapCfg` table, just using the same indirect as we defined on CM, but using different indexes

TagOID 1:

Field | Value | Description
--- | --- | ---
Index TagOID | .1.3.6.1.4.1.9.9.166.1.6.1.1.1 | Index TagOID to retrieve the PolicyMap Name
IndexFormat | --


![cbqospolicymapname_meas_index]


**(\*\*) Dependency**

In this case, we need to retrieve both indexes from the IDX{2}, the complete CMIndex `Service Policy Index`.`Object Index`


The logic is:

- From BaseOID, we will retrieve the desired parent index:

```
> cbQosParentObjectsIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.4
...
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1045] = Gauge32: 1043
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1051] = Gauge32: 1043
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1101] = Gauge32: 1099
...
```

- On the first TagOID, it will try to retrieve the ObjectIndex, but with a modified IDX:

```
> ${IDX1|DOT[0:0]|STRING}.$VAL1
[.1043][.1043]
[.1043][.1043]
[.1099][.1099]


> cbQosConfigIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.2

...
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1043] = Gauge32: 1035
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1109] = Gauge32: 1027
...

```
- And finally, the last TagOID will retrieve the policyNameMap

```
> cbQosPolicyMapName - 1.3.6.1.4.1.9.9.166.1.6.1.1.1

1.3.6.1.4.1.9.9.166.1.6.1.1.1[.1035] = STRING: LAN_Out
1.3.6.1.4.1.9.9.166.1.6.1.1.1[.1063] = STRING: CPP
```

The final index will be the Base with the retrieved tags from policy name:

```
[.1043][.1045] = "LAN_Out"
[.1043][.1051] = "LAN_Out"
[.1099][.1101] = "CPP"
```

At this point, the IDX{3}, will be, with heridated tags from IDX{2}:


```
> Index: IDX{2}

[.1043][.1045] = "Te1/0/3|2|ICMP"           || ifName | policyDirection | cmName
[.1043][.1051] = "Te1/0/3|2|class-default"  || ifName | policyDirection | cmName
[.1099][.1101] = "Te1/0/3|1|NonLocal"       || ifName | policyDirection | cmName

> Index: IDX{3} | cbQosPolicyMapName | IDX{2};DOT[0:1];SKIP

[.1043][.1045] = "Te1/0/3|2|ICMP|LAN_Out"           || ifName | policyDirection | cmName | policyMapName
[.1043][.1051] = "Te1/0/3|2|class-default|LAN_Out"  || ifName | policyDirection | cmName | policyMapName
[.1099][.1101] = "Te1/0/3|1|NonLocal|CPP"       || ifName | policyDirection | cmName | policyMapName

```

### 1.2.6 Index 4 | ClassMap Tag: cbQosCMInfo

Once we have mapped the CMName and its related PolicyMap Name, we can also retrieve the extra info: `cbQosCMInfo - .1.3.6.1.4.1.9.9.166.1.7.1.1.3`, already present in table of `cbQosClassMapCfg - 1.3.6.1.4.1.9.9.166.1.7.1.1`, so the way to configure the measurement is exactly the same as `cbQosCMName` with the same config index.
A simple walk over `cbQosCMInfo`:

```
> cbQosCMInfo - .1.3.6.1.4.1.9.9.166.1.7.1.1.3

.1.3.6.1.4.1.9.9.166.1.7.1.1.3[.1025] = INTEGER: matchAny(3)
.1.3.6.1.4.1.9.9.166.1.7.1.1.3[.1029] = INTEGER: matchAll(2)
.1.3.6.1.4.1.9.9.166.1.7.1.1.3[.1057] = INTEGER: matchAny(3)
```

As we have an indirect tag, we will create an index with "(snmp Table) Indexed with indirect TAG"

![mi-indexed_it]

We fill with the index with following field/values:

Field | Value | Description
--- | --- | ---
Index Label | cbQosCMInfo | Unique SNMPCollector internal identifier of measurement
Index Description | Index to retrieve cbQosCMInfo| --
Index Base OID | .1.3.6.1.4.1.9.9.166.1.5.1.1.2 | Base OID is the `cbQosConfigIndex` which gives us the value of the config related with `cbQosCMInfo` table
Index Tag OID | .1.3.6.1.4.1.9.9.166.1.7.1.1.3 | `cbQosCMInfo` TagOID that will match with `cbQosConfigIndex` result
Index Tag | cmInfo | We will use directly the name of the field that is being retrieved, but you can set whatever you want as your Tag Key
Index Tag Format | -- | We don't need to format the policyDirection tag, but you are free to set up whatever you want!
Index Dependency | IDX{3};DOT[0:1];SKIP | (*)

<p align="center">
   :information_source: Index 4 - cbQosCMInfo
</p>

**(*) Dependency**

In this case, as the matching index is the full index and it will just heridate the IDX{3} tags


![cbqoscminfo_meas]



At this point, the IDX{4}, will be, with heridated tags from IDX{3}:


```
> Index: IDX{3}

[.1043][.1045] = "Te1/0/3|2|ICMP|LAN_Out"           || ifName | policyDirection | cmName | policyMapName
[.1043][.1051] = "Te1/0/3|2|class-default|LAN_Out"  || ifName | policyDirection | cmName | policyMapName
[.1099][.1101] = "Te1/0/3|1|NonLocal|CPP"       || ifName | policyDirection | cmName | policyMapName


> Index: IDX{4} | cbQosCMInfo | IDX{3};DOT[0:1];SKIP

...
[.1043][.1045] = "Te1/0/3|2|ICMP|LAN_Out|"           || ifName | policyDirection | cmName | policyMapName | cbQosCMInfo|3
[.1043][.1051] = "Te1/0/3|2|class-default|LAN_Out|2"  || ifName | policyDirection | cmName | policyMapName | cbQosCMInfo
[.1099][.1101] = "Te1/0/3|1|NonLocal|CPP|3"       || ifName | policyDirection | cmName | policyMapName |cbQosCMInfo
...
```

### 1.2.7 Result

Finally, we setup the result of our multiindex.

As we have heredated all tags until the last index, we only need to setup up the result as:

Field | Value | Description
--- | --- | ---
Result | IDX{4} | Result of the multiindex

And the final pair of `[index]:[TagValues]` will be:

```
...
[.1043][.1045] = "Te1/0/3|2|ICMP|LAN_Out|2"
[.1043][.1051] = "Te1/0/3|2|class-default|LAN_Out|2"
[.1099][.1101] = "Te1/0/3|1|NonLocal|CPP|3"
...
```

## 1.3 Configuring filters

As the base OID of our CMStats is directly the `Service Policy Index`.`Object Index` based on `cbQosConfigIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.2` it will generate an entry for each object index, but not only the related with CM, also the PolicyMap, MatchStmt and all available object in all stats table.

In order to retrieve only the desired one based on what we are retrieving, we will define a `Measurement Filter`


### 1.3.1 OID Condition - Filter classmap


We want to filter only classmap objects, so we need to define a new condition over the OID `cbQosObjectsType - .1.3.6.1.4.1.9.9.166.1.5.1.1.3`

A simple walk over this OID:

```
> cbQosObjectsType - .1.3.6.1.4.1.9.9.166.1.5.1.1.3

.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1043] = INTEGER: policymap(1)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1045] = INTEGER: classmap(2)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1047] = INTEGER: trafficShaping(6)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1049] = INTEGER: queueing(4)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1051] = INTEGER: classmap(2)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1053] = INTEGER: trafficShaping(6)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1085] = INTEGER: queueing(4)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1099][.1099] = INTEGER: policymap(1)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1099][.1101] = INTEGER: classmap(2)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1099][.1103] = INTEGER: trafficShaping(6)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1099][.1105] = INTEGER: police(7)
....

```

And filtered by desired one: `classmap(2)`:

```
...
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1045] = INTEGER: classmap(2)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1051] = INTEGER: classmap(2)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1099][.1101] = INTEGER: classmap(2)
...
```

Go to menu and click over `OID Condition` section
Create a `+ New` OID Condition and fill it with:

Field | Value | Description
--- | --- | ---
ID | cbQosObjectsType_classmap | The unique internal ID to identify the OID Condition
Is Multiple | false | It allows to create a OID condition based on already defined ones
OID | .1.3.6.1.4.1.9.9.166.1.5.1.1.3 | The OID to filter based on object type
CondType | (neq) numeric equal | As the output is an integer, we will select number equal as condition type
CondValue | 2 | Filter only the class objects

<p align="center">
   :information_source: OIDCondition to filter classmap objects
</p>


### 1.3.2 MeasurementFilter - Filter classmap

Now, we want to create a new measurement filter with already created OID condition

Go to menu and click over `Measurement Filters` section
Create a `+ New` OID Condition and fill it with:

Field | Value | Description
--- | --- | ---
ID | cisco_qos_cbQosCMStats_classmap | The unique internal ID to identify the Measurement Filter
Measurements | cisco_qos_cbQosCMStats..cbQosPolicyMapName	 | We will select the internal index of policyMapName
Filter type | OID Condition | Filter type
Oid Condition |cbQosObjectsType_classmap | The already created OID Condition only to filter classmap objects

<p align="center">
   :information_source: Measurement filter to apply OIDCondition on CMStats
</p>


## 1.4 Configuring Measurement Group

Go to menu and click over `Measurement Filters` section
Create a `+ New` Measurement Group and fill it with:

Field | Value | Description
--- | --- | ---
ID | Cisco_QOS | The unique internal ID to identify the Measurement Group
Measurements | cisco_qos_cbQosCMStats	 | Measurement from cisco_qos_cbQosCMStats

<p align="center">
   :information_source: Cisco_QOS measurement group with CMStats
</p>


## 1.5 Configuring Device

Attach your new measurement group and filters to the existing Device with QoS enabled


## 1.6 Reload configuration

In order to reload configuration you must click on the menu item `Reload Config`. Once the SNMP Collector will receive the signal and it will try to reload all configuration. During that process you won't be able to navigate until the process is finished. 


## 1.7 Check your results!

See what are you collecting from the Runtime component!
- Click on `Runtime` menu item
- Find your device and check  if its actived and connected. If not, check your connection data!
- Click  on the eye icon and see what are you collecting!!!


![runtime_qoscm]


# 2. TSStats

The first set of data that we want to retrieve are the TSStats

## 2.1 Configuring metrics

Available metrics are defined on `cbQosCMStatsTable - 1.3.6.1.4.1.9.9.166.1.15.1.1`.
In our example we will configure the following metrics:

ID | Field Name | IsTag | DataSrcType | GetRate | Base OID/Extradata | Scale | Shift | 
---|----------- | ----------- | ----- | ------------------ | ------- | ----- | ----- |
cisco_cbQosTSStatsDelayedByte64 | cbQosTSStatsDelayedByte64	| false | COUNTER64 | false |.1.3.6.1.4.1.9.9.166.1.19.1.1.3	|		0 | 0
cisco_cbQosTSStatsDelayedPkt64 | 	cbQosCMPrePolicyByte64 | false| COUNTER64 | false | .1.3.6.1.4.1.9.9.166.1.19.1.1.6 | 0 | 0
cisco_cbQosTSStatsDropByte64 |	cbQosCMPrePolicyBitRate | false | COUNTER64 | false | .1.3.6.1.4.1.9.9.166.1.19.1.1.9	| 0 | 0

As we have said, each metric will have a double index: `[Service Policy Index].[Object Index]`

## 1.2 Configuring measurements

Let's start to define the measurement

### 1.2.1 Overview

As we have defined, the TSStats needs the following tags to identify each metric:
- Interface
- Direction
- PolicyMap Name
- ClassMap Name

> Note that as a TS is related with ClassMap, we will retrieve also it



### 2.2.2 Index 0 | Service Policy Index Tag: cbQosIfIndex 

It is the same configuration as CMStats

The final output map of pairs [index]:[tag] will be:

```
> Index: IDX{0} | cbQosIfIndex

[.1043] = "Te1/0/3" || ifName
[.1099] = "Te1/0/3" || ifName
```

### 2.2.3 Index 1 | Service Policy Index Tag: cbQosPolicyDirection 

It is the same configuration as CMStats

At this point, we will have that the `IDX{1}` will be:

```
> Index: IDX{0} | cbQosIfIndex

[.1043] = "Te1/0/3" || ifName
[.1099] = "Te1/0/3" || ifName

> Index: IDX{1} | cbQosPolicyDirection | IDX{0};DOT[0:0];SKIP

[.1043] = "Te1/0/3|2" || ifName | policyDirection
[.1099] = "Te1/0/3|1" || ifName | policyDirection
```


### 2.2.4 Index 2 | ClassMap Tag: cbQosCMName

As we have done on CMStats with PolicyMap name, as ClassMap is another object type, we need to iterate over relations OID - `> cbQosParentObjectsIndex - 1.3.6.1.4.1.9.9.166.1.5.1.1.4` in order to identify the related ClassMap

In this case, filtered by the matchstmt objects:

```
> cbQosParentObjectsIndex - 1.3.6.1.4.1.9.9.166.1.5.1.1.4

...
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1043] = Gauge32: 0
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1045] = Gauge32: 1043 # classmap
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1047] = Gauge32: 1045 # ts
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1051] = Gauge32: 1043 # classmap
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1053] = Gauge32: 1051 # ts
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1099] = Gauge32: 0 # policymap
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1101] = Gauge32: 1099 # classmap
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1103] = Gauge32: 1101 # ts
...
```

So, as we have done on CMStats, we need to make a multiple indrection in order to retrieve the classmap name, so, we will create an as **"(snmp Table) Indexed with multiple indirect TAG"**

![mi-indexed_mit]

We fill the index with the following field/values:

Field | Value | Description
--- | --- | ---
Index Label | cbQosCMName | Unique SNMPCollector internal identifier of measurement
Index Description | Index to retrieve cbQosCMName| --
Index Base OID | .1.3.6.1.4.1.9.9.166.1.5.1.1.4 | Base OID is the `cbQosParentObjectsIndex` which gives us the value of the config related with `Object Index` from `ClassMap`
Index Tag | cmName | We will use directly the name of the field that is being retrieved, but you can set whatever you want as your Tag Key
Index Tag Format | -- | We don't need to format the policyDirection tag, but you are free to set up whatever you want!
Multi Tag OID | (*) | Multiple indirects to obtain the ClassMapName and keep it on ClassMap pair of `Service Policy Index`.`Object Index`
Index Dependency | IDX{1};DOT[0:0];SKIP | (**)

<p align="center">
   :information_source: Index 2 - cbQosCMName
</p>


![cbqoscmname_ts_meas]


**(*) Multi Tag OID**

We will define 2 TagOID:

***TagOID 0***:

Field | Value | Description
--- | --- | ---
Index TagOID | .1.3.6.1.4.1.9.9.166.1.5.1.1.2 | Index TagOID to retrieve the `Object Index` from `ClassMap`
IndexFormat | `${IDX1\|DOT[0:0]\|STRING}.$VAL1`

This IndexFormat will modify the check condition and will create the desired index in order keep iterating over indirect tables. Following with the example:


The generated index is just the `Service Policy Index`.`Object Index` from `ClassMap`, so we can use this to retrieve any parameter from `ClassMapCFG` table, but using different indexes

***TagOID 1***:

Field | Value | Description
--- | --- | ---
Index TagOID | .1.3.6.1.4.1.9.9.166.1.7.1.1.1 | Index TagOID to retrieve the ClassMap Name
IndexFormat | --


![cbqoscmname_meas_index]


**(\*\*) Dependency**

In this case, we will retrieve the  to retrieve only the `Service Policy Index` from IDX{1}

The logic is:

- From BaseOID, we will retrieve the desired parent index:

```
> cbQosParentObjectsIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.4
...
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1047] = Gauge32: 1045
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1053] = Gauge32: 1051
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1103] = Gauge32: 1101
...
```

- On the first TagOID, it will try to retrieve the ObjectIndex, but with a modified IDX:

```
> ${IDX1|DOT[0:0]|STRING}.$VAL1
[.1043][.1045]
[.1043][.1051]
[.1099][.1101]


> cbQosConfigIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.2

...
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1045] = Gauge32: 1029
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1051] = Gauge32: 1025
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1101] = Gauge32: 1057
...
```

- And finally, the last TagOID will retrieve the CMName

```
> cbQosCMName - .1.3.6.1.4.1.9.9.166.1.7.1.1.1

.1.3.6.1.4.1.9.9.166.1.7.1.1.1[.1025] = STRING: class-default
.1.3.6.1.4.1.9.9.166.1.7.1.1.1[.1029] = STRING: ICMP
.1.3.6.1.4.1.9.9.166.1.7.1.1.1[.1057] = STRING: NonLocal
```

The final index will be the Base with the retrieved tags from classmap name:

```
[.1043][.1047] = "ICMP"
[.1043][.1053] = "class-default"
[.1099][.1103] = "NonLocal"
```

At this point, the IDX{2}, will be, with heridated tags from IDX{1}:

```
> Index IDX{1}:

[.1043] = "Te1/0/3|2" || ifName | policyDirection
[.1099] = "Te1/0/3|1" || ifName | policyDirection

> Index: IDX{2} | cbQosCMName | IDX{1};DOT[0:0];SKIP

>> DOT[0:0] = [.1043] / [.1099]

[.1043][.1047] = "Te1/0/3|2|ICMP"           || ifName | policyDirection | cmName
[.1043][.1053] = "Te1/0/3|2|class-default"  || ifName | policyDirection | cmName
[.1099][.1103] = "Te1/0/3|1|NonLocal"       || ifName | policyDirection | cmName

```

### 2.2.4 Index 3 | PolicyMap Tag: policyMapName

As we have done on the previous index, we will try to retrieve also the  PolicyMap name, in this case, we need a double iteration to retrieve the PolicyMap parents from ClassMap- `> cbQosParentObjectsIndex - 1.3.6.1.4.1.9.9.166.1.5.1.1.4` in order to identify the related ClassMap

In this case, filtered by the matchstmt objects:


```
> cbQosParentObjectsIndex - 1.3.6.1.4.1.9.9.166.1.5.1.1.4

...
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1043] = Gauge32: 0
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1045] = Gauge32: 1043 # classmap
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1047] = Gauge32: 1045 # ts
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1051] = Gauge32: 1043 # classmap
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1053] = Gauge32: 1051 # ts
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1099] = Gauge32: 0 # policymap
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1101] = Gauge32: 1099 # classmap
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1103] = Gauge32: 1101 # ts
...
```

To do that, we will repeat the step on the Index 2, but in this case we will add a new Tag OID to go over PolicyMap objects instead of ClassMap objects


we will create an as **"(snmp Table) Indexed with multiple indirect TAG"**

![mi-indexed_mit]

We fill the index with the following field/values:

Field | Value | Description
--- | --- | ---
Index Label | cbQosPolicyMapName | Unique SNMPCollector internal identifier of measurement
Index Description | Index to retrieve cbQosPolicyMapName| --
Index Base OID | .1.3.6.1.4.1.9.9.166.1.5.1.1.4 | Base OID is the `cbQosParentObjectsIndex` which gives us the value of the config related with `Object Index` from `PolicyMap`
Index Tag | cmName | We will use directly the name of the field that is being retrieved, but you can set whatever you want as your Tag Key
Index Tag Format | -- | We don't need to format the policyDirection tag, but you are free to set up whatever you want!
Multi Tag OID | (*) | Multiple indirects to obtain the ClassMapName and keep it on ClassMap pair of `Service Policy Index`.`Object Index`
Index Dependency | IDX{2};DOT[0:1];SKIP | (**)

<p align="center">
   :information_source: Index 3 - cbQosPolicyMapName
</p>


![cbqospolicyname_ts_meas]


**(*) Multi Tag OID**

We will define 2 TagOID:

***TagOID 0***:

Field | Value | Description
--- | --- | ---
Index TagOID | .1.3.6.1.4.1.9.9.166.1.5.1.1.4 | Index TagOID to retrieve the `Parent Object` from `ClassMap`
IndexFormat | `${IDX1\|DOT[0:0]\|STRING}.$VAL1`

This IndexFormat will modify the check condition and will create the desired index in order keep iterating over indirect tables. Following with the example:

The generated index is just the `Service Policy Index`.`Object Index` from `ClassMap`, so we can use this to retrieve the index of `PolicyMap` related with this `ClassMap` table, but using different indexes


***TagOID 1***:

Field | Value | Description
--- | --- | ---
Index TagOID | .1.3.6.1.4.1.9.9.166.1.5.1.1.2 | Index TagOID to retrieve the `Object Index` from `PolicyMap`
IndexFormat | `${IDX1\|DOT[0:0]\|STRING}.$VAL1`

This IndexFormat will modify the check condition and will create the desired index in order keep iterating over indirect tables. Following with the example:


The generated index is just the `Service Policy Index`.`Object Index` from `ClassMap`, so we can use this to retrieve any parameter from `ClassMapCFG` table, but using different indexes

***TagOID 2***:

Field | Value | Description
--- | --- | ---
Index TagOID | .1.3.6.1.4.1.9.9.166.1.6.1.1.1 | Index TagOID to retrieve the PolicyMap Name
IndexFormat | --


![cbqoscmname_meas_index_ts]


**(\*\*) Dependency**


In this case, we need to retrieve both indexes from the IDX{2}, the complete CMIndex `Service Policy Index`.`Object Index`

The logic is:

- From BaseOID, we will retrieve the desired parent index:

```
> cbQosParentObjectsIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.4
...
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1047] = Gauge32: 1045
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1043][.1053] = Gauge32: 1051
1.3.6.1.4.1.9.9.166.1.5.1.1.4[.1099][.1103] = Gauge32: 1101
...
```

- On the first TagOID, it will try to retrieve the cbQosParentObjectsIndex, but with a modified IDX, so we will match the related ClassMap

```
> ${IDX1|DOT[0:0]|STRING}.$VAL1
[.1043][.1045]
[.1043][.1051]
[.1099][.1101]


> cbQosParentObjectsIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.4

...
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1045] = Gauge32: 1043
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1051] = Gauge32: 1043
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1101] = Gauge32: 1099
...

```

- On the second TagOID, it will try to retrieve the ObjectIndex, but with a modified IDX:

```
> ${IDX1|DOT[0:0]|STRING}.$VAL1
[.1043][.1043]
[.1043][.1043]
[.1099][.1099]


> cbQosConfigIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.2

...
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1043][.1043] = Gauge32: 1035
.1.3.6.1.4.1.9.9.166.1.5.1.1.2[.1099][.1099] = Gauge32: 1027
...

```
- And finally, the last TagOID will retrieve the policyNameMap

```
> cbQosPolicyMapName - 1.3.6.1.4.1.9.9.166.1.6.1.1.1

1.3.6.1.4.1.9.9.166.1.6.1.1.1[.1035] = STRING: LAN_Out
1.3.6.1.4.1.9.9.166.1.6.1.1.1[.1063] = STRING: CPP
```

The final index will be the Base with the retrieved tags from policy name:

```
[.1043][.1047] = "LAN_Out"
[.1043][.1053] = "LAN_Out"
[.1099][.1103] = "CPP"
```

At this point, the IDX{3}, will be, with heridated tags from IDX{2}:


```
> Index: IDX{2}

[.1043][.1047] = "Te1/0/3|2|ICMP"           || ifName | policyDirection | cmName
[.1043][.1053] = "Te1/0/3|2|class-default"  || ifName | policyDirection | cmName
[.1099][.1103] = "Te1/0/3|1|NonLocal"       || ifName | policyDirection | cmName

> Index: IDX{3} | cbQosPolicyMapName | IDX{2};DOT[0:1];SKIP

[.1043][.1047] = "Te1/0/3|2|ICMP|LAN_Out"           || ifName | policyDirection | cmName | policyMapName
[.1043][.1053] = "Te1/0/3|2|class-default|LAN_Out"  || ifName | policyDirection | cmName | policyMapName
[.1099][.1103] = "Te1/0/3|1|NonLocal|CPP"       || ifName | policyDirection | cmName | policyMapName

```

### 2.2.4 Result

Finally, we setup the result of our multiindex.

As we have heredated all tags until the last index, we only need to setup up the result as:

Field | Value | Description
--- | --- | ---
Result | IDX{3} | Result of the multiindex

And the final pair of `[index]:[TagValues]` will be:

```
...
[.1043][.1047] = "Te1/0/3|2|ICMP|LAN_Out"
[.1043][.1053] = "Te1/0/3|2|class-default|LAN_Out"
[.1099][.1103] = "Te1/0/3|1|NonLocal|CPP"
...
```

## 2.3 Configuring filters

As the base OID of our CMStats is directly the `Service Policy Index`.`Object Index` based on `cbQosConfigIndex - .1.3.6.1.4.1.9.9.166.1.5.1.1.2` it will generate an entry for each object index, but not only the related with CM, also the PolicyMap, MatchStmt and all available object in all stats table.

In order to retrieve only the desired one based on what we are retrieving, we will define a `Measurement Filter`


### 2.3.1 OID Condition - Filter TS


We want to filter only classmap objects, so we need to define a new condition over the OID `cbQosObjectsType - .1.3.6.1.4.1.9.9.166.1.5.1.1.3`

A simple walk over this OID:

```
> cbQosObjectsType - .1.3.6.1.4.1.9.9.166.1.5.1.1.3

.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1043] = INTEGER: policymap(1)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1045] = INTEGER: classmap(2)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1047] = INTEGER: trafficShaping(6)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1049] = INTEGER: queueing(4)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1051] = INTEGER: classmap(2)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1053] = INTEGER: trafficShaping(6)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1085] = INTEGER: queueing(4)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1099][.1099] = INTEGER: policymap(1)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1099][.1101] = INTEGER: classmap(2)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1099][.1103] = INTEGER: trafficShaping(6)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1099][.1105] = INTEGER: police(7)
....

```

And filtered by desired one: `trafficShaping(6)`:

```
...
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1047] = INTEGER: trafficShaping(6)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1043][.1053] = INTEGER: trafficShaping(6)
.1.3.6.1.4.1.9.9.166.1.5.1.1.3[.1099][.1103] = INTEGER: trafficShaping(6)
...
```

Go to menu and click over `OID Condition` section
Create a `+ New` OID Condition and fill it with:

Field | Value | Description
--- | --- | ---
ID | cbQosObjectsType_trafficShaping| The unique internal ID to identify the OID Condition
Is Multiple | false | It allows to create a OID condition based on already defined ones
OID | .1.3.6.1.4.1.9.9.166.1.5.1.1.3 | The OID to filter based on object type
CondType | (neq) numeric equal | As the output is an integer, we will select number equal as condition type
CondValue | 6 | Filter only the class objects

<p align="center">
   :information_source: OID Condition to filter trafficShaping
</p>


### 2.3.2 MeasurementFilter - Filter classmap

Now, we want to create a new measurement filter with already created OID condition

Go to menu and click over `Measurement Filters` section
Create a `+ New` OID Condition and fill it with:

Field | Value | Description
--- | --- | ---
ID | cisco_qos_cbQosTSStats_trafficShaping | The unique internal ID to identify the Measurement Filter
Measurements |cisco_qos_cbQosTSStats..cbQosPolicyMapName | We will select the internal index of policyMapName
Filter type | OID Condition | Filter type
Oid Condition |cbQosObjectsType_trafficShaping | The already created OID Condition only to filter classmap objects

<p align="center">
   :information_source: Measurement filter to apply OIDCondition on TSStats
</p>

## 2.4 Configuring Measurement Group

Go to menu and click over `Measurement Filters` section
Create a `+ New` Measurement Group and fill it with:

Field | Value | Description
--- | --- | ---
ID | Cisco_QOS | The unique internal ID to identify the Measurement Group
Measurements | cisco_qos_cbQosTSStats	 | Measurement from cisco_qos_cbQosTSStats

<p align="center">
   :information_source: Cisco_QOS measurement group with TSStats
</p>



## 2.5 Configuring Device

Attach your new measurement group and filters to the existing Device with QoS enabled


## 2.6 Reload configuration

In order to reload configuration you must click on the menu item `Reload Config`. Once the SNMP Collector will receive the signal and it will try to reload all configuration. During that process you won't be able to navigate until the process is finished. 


## 2.7 Check your results!

See what are you collecting from the Runtime component!
- Click on `Runtime` menu item
- Find your device and check  if its actived and connected. If not, check your connection data!
- Click  on the eye icon and see what are you collecting!!!


![runtime_qosts]
