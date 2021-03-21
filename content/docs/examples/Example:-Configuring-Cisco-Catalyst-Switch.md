# Example: Configuring Cisco Catalyst with basic metrics

This example will show up how to configure a Cisco Catalyst on snmpcollector

## 0. Prerequisites : What and from we are going to collect our data?

### 0.1 SNMP Device model
In order to select the correct metrics for our device, we must check first what model it is.
If you don't know the model, you can pre-add the device on SNMP Device Component and do a test connection. To do that:
- Click on `SNMP Device` menu item
- A table with configured devices will show up. Press "New" button or edit an existing one to access to the configuration form.
- Fill the device with the basic connection information
- If everything is OK, you will be able to do a "Test Connection". This button will show up a console with System information  of the Device. 

### 0.2 Select metrics to collect data

The first step is selecting what metrics we want to collect. For this we can check for Cisco docs and defined MIBs like:
- [Cisco Doc](http://www.cisco.com/c/en/us/td/docs/net_mgmt/prime/infrastructure/3-1/user/guide/pi_ug/mon-pol-thresh.html)
- [Cisco Example](http://www.cisco.com/c/en/us/support/docs/ip/simple-network-management-protocol-snmp/15215-collect-cpu-util-snmp.pdf)

In this example, we are going to collect the following metrics to provide basic interface and performance status of our switch.

- **Basic Metrics**

Metric Name | MIB | OID | Indexed 
----------------|---------------- | ---------------- | ---------------- 
ifHCInOctets | IF-MIB | .1.3.6.1.2.1.31.1.1.1.6 | ifName 
ifHCOutOctets | IF-MIB | .1.3.6.1.2.1.31.1.1.1.10 | ifName
ifHighSpeed | IF-MIB | .1.3.6.1.2.1.31.1.1.1.15 | ifName
ifAlias | IF-MIB | .1.3.6.1.2.1.31.1.1.1.18 | ifName
ciscoMemoryPoolUsed | CISCO-MEMORY-POOL-MIB | .1.3.6.1.4.1.9.9.48.1.1.1.5 | ciscoMemoryPoolName
ciscoMemoryPoolFree | CISCO-MEMORY-POOL-MIB | .1.3.6.1.4.1.9.9.48.1.1.1.6 | ciscoMemoryPoolName
cpmCPUTotal1minRev | CISCO-MEMORY-POOL-MIB | .1.3.6.1.4.1.9.9.109.1.1.1.1.7 | cpmCPUTotalPhysicalIndex (depending on model)

<p align="center">
   :information_source: Basic Metrics table
</p>

In order to give more generic information, we will define the following extended metrics. On the next point we will see how to configure them.

- **Extended Metrics**

Metric Name | Formula | Indexed 
----------------|---------------- | ----------------
InputUtilization| ((ifHCInOctets * 8)/(ifHighSpeed * 1000000)) * 100 | .1.3.6.1.2.1.31.1.1.1.10 | ifName 
OutputUtilization | ((ifHCOutOctets * 8)/(ifHighSpeed * 1000000)) * 100 | .1.3.6.1.2.1.31.1.1.1.10 | ifName
ciscoMemoryPool_utilization_percent | (ciscoMemoryPoolUsed/ciscoMemoryPoolFree+ciscoMemoryPoolUsed) * 100 | .1.3.6.1.2.1.31.1.1.1.15 | ifName

<p align="center">
  :information_source: Extended Metric table
</p>

## 1. Configuring Metrics
Once we have defined our metrics its time to configure them on our SNMPCollector Instance:
- Click on `SNMP Metric` menu item
- Click `+New` button. The metric form will show up.

_Notes:_
- On `Counter32/64` metric type you can define `COUNTER32/64` as DataSrcType, which computes the difference between iterations. It only works with really counter behaivour. 
- `IsTag` option allows to set the value as tag. The tag key will be defined as `Field Name`
- `GetRate` option only works when `COUNTERNN` as DataSrcType. It divides the result of the computed difference by the elapsed time between polls.
- Remember that you can always test your OIDs on SNMP Console from our pre-added SNMP Device

_Recomendations:_
- In order to identify all your components IDs, we recommend you to set some prefix when you are configuring them. This will allow to find faster and set up devices easier. In this example, if.* Metrics are coming from a MIB that can be shared for more than one device type. For the CPU/Mem metrics, we will use the prefix `Cisco_Catalyst`

### 1.1 Configure Basic metrics 

ID | Field Name | IsTag | DataSrcType | GetRate | Base OID/Extradata | Scale | Shift | 
---|----------- | ----------- | ----- | ------------------ | ------- | ----- | ----- |
ifHCInOctets | ifHCInOctets | false | COUNTER64 | true | .1.3.6.1.2.1.31.1.1.1.6 | 0 | 0
ifHCOutOctets | ifHCOutOctets | false | COUNTER64 | true | .1.3.6.1.2.1.31.1.1.1.10 | 0 | 0
ifHighSpeed | ifHighSpeed | false | Gauge32| true | .1.3.6.1.2.1.31.1.1.1.15 | 0 | 0
ifAlias | ifAlias | true | OCTETSTRNG | -- | .1.3.6.1.2.1.31.1.1.1.18 | 0 | 0
Cisco_Catalyst_ciscoMemoryPoolUsed | memUsed | false | Gauge32 | -- | .1.3.6.1.4.1.9.9.48.1.1.1.5 | 0 | 0
Cisco_Catalyst_ciscoMemoryPoolFree | memFree | false | Gauge32 | -- | .1.3.6.1.4.1.9.9.48.1.1.1.6 | 0 | 0
Cisco_Catalyst_cpmCPUTotal1minRev | cpuUsed  | false | Gauge32 | -- | .1.3.6.1.4.1.9.9.109.1.1.1.1.7 | 0 | 0

<p align="center">
  :information_source: SNMP Basic Metrics configuration
</p>

Once you get all your basic metrics, it is time to configure the extended ones:

### 1.2 Configure Extended metrics 

ID | Field Name | IsTag | DataSrcType | GetRate | Base OID/Extradata | Scale | Shift | 
---|----------- | ----------- | ----- | ------------------ | ------- | ----- | ----- |
InputUtilization| InputUtilization | false | STRINGEVAL | -- | ((ifHCInOctets * 8)/(ifHighSpeed * 1000000)) * 100 | 0 | 0 
OutputUtilization | OutputUtilization | false | STRINGEVAL | -- | ((ifHCOutOctets * 8)/(ifHighSpeed * 1000000)) * 100 | 0 | 0
Cisco_ciscoMemoryPool_utilization_percent | memUtilization | false | STRINGEVAL | -- |(ciscoMemoryPoolUsed/ciscoMemoryPoolFree+ciscoMemoryPoolUsed) * 100 | 0 | 0

<p align="center">
  :information_source: SNMP Extended Metrics configuration
</p>

## 2. Configuring  Measurements

Once we have our metrics configured, its time to define where and how we want to store them in InfluxDB data structure: measurements. 

_Recomendations:_
- If a collection of metrics are defined with the same index OID, try to set up them on the same measurement. This will reduce the number of walks to create the indexes and filterings rules.
- Try to define the measurement name with a easy identifier to make configuring the devices easier for you.
- If you are going to define metrics with 'nonZero' report, remember that if the metric is not already defined on InfluxDB you won't be able to query it. We recommend you to create the metrics for few minutes and then change the report status to desired `nonZero`.

To create a measurement:
- Click on Measurement menu item
- Click `+New` button. The measurement form will show up.

### 2.1 ifMIB Measurement

The following metrics have the same index on the table and are coming from the same MIB, so we create a new measurement with ifMIB basic + extended metrics.

In our example, we are only interested of computed metrics, so, we need set the right report status of each one:
<dd>:no_entry_sign:-ifHCInOctets</dd>
<dd>:no_entry_sign:-ifHCOutOctets</dd>
<dd>:no_entry_sign:-ifHighSpeed</dd>
<dd>:no_entry_sign:-ifAlias</dd>
<dd>:white_check_mark:-InputUtilization</dd>
<dd>:white_check_mark:-OutputUtilization</dd>
<br>
_Notes_:

- As the defined extended metrics are computed, we need to add relational needed metrics on the same measurement.

We define the Cisco_ifMIB_Basics measurement which will group all metrics. Remember that they are indexed with the name of the interfaces, so we need to set up like this:

ID | Name | GetMode | Index OID | Tag OID | Index Tag | Index Tag Format | Index as Value | Metric Fields
---|------| ------- | --------- | --------| --------- | ---------------- | -------------- | ------------ |
Cisco_ifMIB_Basics | cisco_ifMib_basics | indexed | .1.3.6.1.2.1.2.2.1.2 | -- | portName | -- | false | _#BASICS_:<ul><li>ifHCInOctets</span></li><li>ifHCOutOctets</li> <li>ifHighSpeed</li><li>ifAlias</li></ul>_#EXTENDED_:<ul><li>InputUtilization</li><li>OutputUtilization</li></ul>

<p align="center">
  :information_source: ifMIB Measurement configuration
</p>

### 2.2 Cisco Memory Measurement

Let's configure the Cisco Memory measurement. As we did on the ifMIB metrics, we need to have clear what metrics are we going to send. In our case, we are interested on send only the % usage of the Memory, so:

<dd>:no_entry_sign:-Cisco_Catalyst_ciscoMemoryPoolUsed </dd>
<dd>:no_entry_sign:-Cisco_Catalyst_ciscoMemoryPoolFree </dd>
<dd>:white_check_mark:-Cisco_ciscoMemoryPool_utilization_percent </dd>
<br/>

So we  create the new Measurement with the following configuration:

ID | Name | GetMode | Index OID | Tag OID | Index Tag | Index Tag Format | Index as Value | Metric Fields
---|------| ------- | --------- | --------| --------- | ---------------- | -------------- | ------------ |
Cisco_Catalyst_ciscoMemoryPool | cisco_catalyst_ciscoMemoryPool | indexed | .1.3.6.1.4.1.9.9.48.1.1.1.2 | -- | memIndex | -- | false | _#BASICS_:<ul><li>Cisco_Catalyst_ciscoMemoryPoolUsed</li><li>Cisco_Catalyst_ciscoMemoryPoolFree</li></ul>_#EXTENDED_:<ul><li>Cisco_ciscoMemoryPool_utilization_percent</li></ul>

<p align="center">
  :information_source: Cisco Memory Measurement configuration
</p>


### 2.3 Cisco CPU Measurement

Our last measurement: Cisco CPU. This OID is indexed by a double index:

In order to identify the physical entity to which these values correspond, we must poll the following MIB Object : cpmCPUTotalPhysicalIndex:

MIB Object | OID | Description
-----------| --- | -----------
cpmCPUTotalPhysicalIndex | 1.3.6.1.4.1.9.9.109.1.1.1.1.2 | Returns the index assigned on entPhysicalName

<p align="center">
  :information_source: cpmCPUTotalPhysicalIndex MIB Object
</p>
With those values we can get the assigned name of each CPU Module from the following OID:

MIB Object | OID | Description
-----------| --- | -----------
entPhysicalName | 1.3.6.1.2.1.47.1.1.1.1.7 | Returns the name of each CPU Module

<p align="center">
  :information_source: entPhysicialName MIB Object
</p>

Once we get our double indexed resolved we can configure our measurement... but before that, we must check out what would happen if the CPU Module are set up with the same name? The SNMPCollector will create 2 points with the same tag/field set, so it will only send 1 value!

In order to solve that, we can add an `Index Tag Format`. The SNMP Collector generates the following vars each time it does an Indexing operation:

- `IndexOID` returns `$IDX1` and `$VAL1`
- `Tag OID` returns `$IDX2` and `$VAL2`

So to distinguish some CPU with the same name we must set up the `Index Tag Format` with `$VAL2 - $IDX1`.

i.e: Tag set : `CPU Module 1 - 8`
- `$VAL2 = CPU Module 1` is the result of the entPhysicalName poll
- `$IDX1 = 8` is the index of entry table.

With all cases checked, we can finally add our measurement:

ID | Name | GetMode | Index OID | Tag OID | Index Tag | Index Tag Format | Index as Value | Metric Fields
---|------| ------- | --------- | --------| --------- | ---------------- | -------------- | ------------ |
Cisco_Catalyst_CiscoProcessMIB | cisco_catalyst_ciscoProcessMib | indexed_it | .1.3.6.1.4.1.9.9.109.1.1.1.1.2 | .1.3.6.1.2.1.47.1.1.1.1.7 | physIndex| $VAL2-$IDX1 | false | _#BASICS_:<ul><li>Cisco_Catalyst_cpmCPUTotal1minRev</li></ul>

<p align="center">
  :information_source: Cisco CPU Measurement configuration
</p>

## 3. Configure Measurement Groups

Now, its time to create a template for all Cisco Catalyst switches!

- Click on `Measurement` Group from menu item
- Press `+New` button or edit an existing one to access to the configuration form.

Measurement Groups is a virtual group of measurements, it allows to have different templates in order to configure devices on a really easy way!

We don't have to  do any especial here, we only must to set up the ID and select the different measurement that are going to be part of our template:

ID | Name 
---|------
Cisco_Catalyst_Template_Basic | <ul><li>Cisco_ifMIB_Basics</li><li>Cisco_Catalyst_ciscoMemoryPool</li><li>Cisco_Catalyst_CiscoProcessMIB </li></ul>

<p align="center">
  :information_source: Cisco_Catalyst_Template Measurement Group configuration
</p>

## 4. Configure OID Conditions

On this point we are now able to poll all data configured in our metrics. But lets do something more complex!
Lets filter what do we want to send, it is time to create some OIDs Conditions!

In our example we will work with the following requirements:
- (1) We only want the interfaces with UP state
- (2) We only want the physical Eth interfaces

So the result would be translated as: we only want those that comply with the following `(1) && (2)`

To do that, we need to create a total of three OID conditions, lets configure all:

- Click on `OID Condtions` item on menu
- Click `+New` button. The measurement form will show up.

### 4.1 Interfaces with UP status condition

The MIB object that returns that information is the ifOperStatus

MIB Object | OID | Description
-----------| --- | -----------
ifOperStatus| 1.3.6.1.2.1.2.2.1.8 | Return the status of the interface: <dd>up(1), down(2), testing(3)</dd> 

<p align="center">
  :information_source: ifOperStatus MIB Object
</p>

We only want the up interfaces so `numerical equal than 1`. Let's create a OID Condition with the following configuration:

ID | Is Multiple | OID | CondType | CondValue
---|------------ | --- | -------- | ---------
ifOperStatus_UP | false | .1.3.6.1.2.1.2.2.1.8 | neq | 1

<p align="center">
  :information_source: ifOperStatus_UP OID Condition configuration
</p>

### 4.2 Interfaces ETH like

The MIB object that returns that information is the ifType

MIB Object | OID | Description
-----------| --- | -----------
ifType| 1.3.6.1.2.1.2.2.1.3 | The type of interface, distinguished according to the physical/link protocol 

<p align="center">
  :information_source: ifType MIB Object
</p>


The eth are `ifType = 6`, so we need to configure a new OID Condition:

ID | Is Multiple | OID | CondType | CondValue
---|------------ | --- | -------- | ---------
ifType_ETH | false | .1.3.6.1.2.1.2.2.1.3 | neq | 6

<p align="center">
  :information_source: ifType_ETH OID Condition configuration
</p>
### 4.3 Mutiple OID Condition: UP && ETH

In order to apply a multiple condition we need to activate the configuration flag `Is Multiple` and set the field `OID Condition` with the logical combination of already created ones:

ID | Is Multiple | OID Condition | CondType | CondValue
---|------------ | --- | -------- | ---------
interfaces_ETH_UP | true| ifOperStatus_UP && ifType_ETH | neq | 6
<p align="center">
  :information_source: interfaces_ETH_UP Multiple Condition configuration
</p>

## 5. Configure Measurement Filters

Once we have our OID Conditions configured, lets put them on a Measurement Filter. The measurement filter is applied on measurement with Indexed/Tag OIDs.

In our case, we need to create the filter in our ifMIB measurement to only get those interfaces that are UP and are Ethernet like.

To to that, lets create a Measurement Filter:

- Click on `Measurement Filters` menu
- Click `+New` button. The filter form will show up.

Lets fill our Measurement filter.

ID | Measurements | Filter Type | OID Condition
---|------------- | ----------- | ------------- 
Cisco_ifMIB_UP_ETH | Cisco_ifMIB_Basics  | OID Condition  | interfaces_ETH_UP  | 6

<p align="center">
  :information_source: Cisco_ifMIB_UP_ETH Measurement Filter configuration
</p>


## 6. Configure SNMP Device

Lets do a check what we have done until now:

- We defined our core basic metrics to get performance and port statistics of our device!
- We defined 3 different measurements with complex indexes that allows to get every entry of metric objects without overlapping!
- We defined a Basic Template that allows us to configure a new device in less than 1 minute!
- We defined 3 OID Conditions and created a filter to only get ETH and UP interfaces!

So its time to add our device and attach our already created components:

_Note_: 
- To simplfy the following configuration, we will skip connection data fields.


ID | ConnectionData | Measurement Groups | Measurement Filters
---|------------- | ----------- | ------------- 
myCisco_Catalyst | .... | Cisco_Catalyst_Template_Basic | Cisco_ifMIB_UP_ETH

<p align="center">
  :information_source: Cisco SNMP Device configuration
</p>


## 7. Reload configuration
In order to reload configuration you must click on the menu item `Reload Config`. Once the SNMP Collector will receive the signal and it will try to reload all configuration. During that process you won't be able to navigate until the process is finished. 


## 8. Check your results!

See what are you collecting from the Runtime component!
- Click on `Runtime` menu item
- Find your device and check  if its actived and connected. If not, check your connection data!
- Click  on the eye icon and see what are you collecting!!!

You must check the Metrics Info panel:
- Check that the are our 3 defined Measurements
- Click on every measurement and check  the created Metrics and its value
- Check the indexes are correct and that the filters are applied correctly on our ifMIB measurement!




