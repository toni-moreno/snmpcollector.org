---
title: "SnmpCollector Automations"
linkTitle: "SnmpCollector Automations"
weight: 8
description: >
---

SnmpCollector lets you do via API everything you can do with the WebUI, in this way you can automate any of your configuration, actions.

You can do it yourself or use any of these other tools.

* https://github.com/ixaustralia/ixaustralia-snmpcollector: thanks to [Tim Raphael](https://github.com/tardoe)
* https://github.com/topranks/mib2SnmpCol : thanks to [Cathal Mooney](https://github.com/topranks)
* https://pypi.org/project/pysnmpcollector/ : a python client lib for SnmpCollector  , thanks to Steffen Schumacher



Here some examples using REST API on how to.

* Do login. ( get session cookie and store on cookies.txt file).

````bash
curl -v -X POST -F "username=USER" -F "password=PASS" -c  cookies.txt http://mysnmpcollector.myorg.org:8090/login
````
* Get all devices status info.

````bash
curl -v -X GET -b cookies.txt http://mysnmpcollector.myorg.org:8090/api/rt/device/info
````
* Get the status for an specific device (needs its deviceid)

````bash
curl  -X GET -b cookies.txt http://mysnmpcollector.myorg.org:8090/api/rt/device/info/<DEVICE_ID>
````

* Do a SNMP connection TEST with  PING  API

```bash

DEVICE_FILE=$(mktemp)
cat << EOF > $DEVICE_FILE
{
  "ID": "SOME_DEVICE",
  "Host": "192.168.24.24",
  "Port": 161,
  "SystemOIDs": null,
  "Retries": 5,
  "Timeout": 20,
  "Repeat": 0,
  "Active": true,
  "SnmpVersion": "2c",
  "Community": "public",
  "V3SecLevel": "",
  "V3AuthUser": "",
  "V3AuthPass": "",
  "V3AuthProt": "",
  "V3PrivPass": "",
  "V3PrivProt": "",
  "V3ContextEngineID": "",
  "V3ContextName": "",
  "DisableBulk": false,
  "MaxRepetitions": 50,
  "Freq": 60,
  "UpdateFltFreq": 60,
  "ConcurrentGather": true,
  "OutDB": "my_DB",
  "LogLevel": "info",
  "LogFile": "",
  "SnmpDebug": false,
  "DeviceTagName": "device",
  "DeviceTagValue": "id",
  "ExtraTags": [
    "tag1=someinfo",
    "tag2=someinfo2"
  ],
  "DeviceVars": [
    ""
  ],
  "Description": "",
  "MeasurementGroups": [
    "MyMeasurmentGroup"
  ],
  "MeasFilters": [
     "MyMeasurement1_filter",
     "MyMeasurement2_filter"
  ]
}
EOF

curl  -X POST -b cookies.txt -d @$DEVICE_FILE http://mysnmpcollector.myorg.org:8090/api/rt/agent/snmpconsole/ping/ --header "Content-Type: application/json"
````

* Add a new device ( supose you have already configured the MeasurementGroups/Filters and InfluxServers)

````bash

DEVICE_FILE=$(mktemp)
cat << EOF > $DEVICE_FILE
{
  "ID": "SOME_DEVICE",
  "Host": "192.168.24.24",
  "Port": 161,
  "SystemOIDs": null,
  "Retries": 5,
  "Timeout": 20,
  "Repeat": 0,
  "Active": true,
  "SnmpVersion": "2c",
  "Community": "public",
  "V3SecLevel": "",
  "V3AuthUser": "",
  "V3AuthPass": "",
  "V3AuthProt": "",
  "V3PrivPass": "",
  "V3PrivProt": "",
  "V3ContextEngineID": "",
  "V3ContextName": "",
  "DisableBulk": false,
  "MaxRepetitions": 50,
  "Freq": 60,
  "UpdateFltFreq": 60,
  "ConcurrentGather": true,
  "OutDB": "my_DB",
  "LogLevel": "info",
  "LogFile": "",
  "SnmpDebug": false,
  "DeviceTagName": "device",
  "DeviceTagValue": "id",
  "ExtraTags": [
    "tag1=someinfo",
    "tag2=someinfo2"
  ],
  "DeviceVars": [
    ""
  ],
  "Description": "",
  "MeasurementGroups": [
    "MyMeasurmentGroup"
  ],
  "MeasFilters": [
     "MyMeasurement1_filter",
     "MyMeasurement2_filter"
  ]
}
EOF

curl  -X POST -b cookies.txt -d @$DEVICE_FILE http://mysnmpcollector.myorg.org:8090/api/cfg/snmpdevice/ --header "Content-Type: application/json"

````


* Generic Import ( Any data )

You can also use curl as a way to import,  previouly exported data in json format with the export web-ui tool.  
````
curl -X POST-b cookies.txt  -F "auto_rename=false" -F "over_write=true" -F "export_file=@/path/where/the/file/is/exported_file.json" http://mysnmpcollector.myorg.org:8090/api/cfg/import
````
