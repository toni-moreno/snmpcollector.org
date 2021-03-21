# Review Logs

snmpcollector has a complete set of log files to review all what is happening while gathering snmp data from our infrastructure. All logs are located in the same directory LOG_DIR.

Default LOG_DIR is at `/var/log/snmpcollector` if snmpcollector has been installed with  debian and redhat based packages. is  at `/opt/snmpcollector/log` in docker and always can be set with -log option  passed to the snmpcollector binary.

If installed with debian/redhat packages you can also tune this parameters in these files

* rpm /etc/sysconfig/snmpcollector
* deb /etc/default/snmpcollector

### Main agent logs

`$LOG_DIR/snmpcollector.log`

Show basic initialisation process and the result of runtime administration from the web ui
Default Level: set in the main config.toml file under the general section
Supported Levels:  panic,fatal,error,warn,info,debug
Can be changed online?: no

### HTTP access logs

`$LOG_DIR/http_access.log`

Show us all http access request the result and response time. This log has not levelling support

### Device specific logs

`$LOG_DIR/<device_id>.log`

This is the main log when you if you have problems with only a set of devices and only under certain conditions.

Default Level: set in the device configuration section on the configuration database
Supported Levels:  panic,fatal,error,warn,info,debug
Can be changed online?: yes in the runtime webui

### SNMP debug logs

`$LOG_DIR/snmpdebug_<device_id>_<measurement_id>.log`

This log is disabled by default and can be enabled online on the webui , when stabilising snmp links with remote devices snmpcollector has one link by measurement. When enabling snmpdebug log each measurement on the device will create a new file with snmp protocol related debug. This debug will help us to review connection and or snmp protocol related problems.

### SQL debug log

`$LOG_DIR/sql.log`

This log is disabled by default.

Default Level: set in the main config.toml file under the general section
Supported Levels:  on/off ( debug = true /  debug = false)
Can be changed online?: no

# Self Monitoring 
When snmpcollector has self-monitoring activate it can send data from itself to the "default" backend (you should have both selfmon active and one influx backend configured with id = "default".

You can activate on the main config file config.toml on the [selfmon] section.


````toml
[selfmon]
 #enable true/false enable/disable self monitoring
 enabled = true
 #send data Frequency
 freq = 60
 #prefix for measurement naming
 prefix = ""
 #inherit device tags (only apply to the selfmon_device_stats measurmentes)
 inheritdevicetags = true
 #adds extra tags to the measurement config should be set as a csv - tag=value1,tag2=value2,...,tagN=valN
 extratags = [ "instance=snmpcollector01" ]
````
When active it will send 2 measurements.

 ### defined measurements

These are the defined measurements, where user can add prefix in the config.toml if needed.

measurement| description
-----------|-------------
<prefix>selfmon_gvm | send statistics about the Go Virtual Machine.
<prefix>selfmon_device_stats | send statistic data form each gathering device
<prefix>selfmon_outdb_stats |statistics measurement for each output db

#### selfmon_gvm

FieldName | Source| Unit |Description
----------|-------|------|-------
__runtime_goroutines__ |runtime.NumGoroutine() | number | Number of currently running goroutines
__mem.alloc__ | runtime.ReadMemStats.Alloc | bytes |Total bytes allocated
__mem.mallocs__ | runtime.ReadMemStats.Mallocs | mallocs per second | Number of Mallocs issued to the system
__mem.frees__ | runtime.ReadMemStats.Frees | frees per second | Number of frees issued to the system
__mem.heapAlloc__ | runtime.ReadMemStats.HeapAlloc | bytes |  allocated heap objects.
__mem.stackInuse__ | runtime.ReadMemStats.StackInuse | | bytes in stack spans.  In-use stack spans have at least one stack in them. These spans can only be used for other stacks of the same size. There is no StackIdle because unused stack spans are returned to the heap (and hence counted toward HeapIdle).
__gc.total_pause_ns__ | memStats.PauseTotalNs | ms | accumulated paused in ms
__gc.pause_per_interval__ |memStats.PauseTotalNs | ms/interval | accumulated paused in ms since last gathered statistic
__gc.pause_per_second__ |memStats.PauseTotalNs | ms/second|  accumulated paused in ms per second (normalized) 
__gc.gc_per_interval__ | memStats.NumGC | #gc/second |number of gc's since last gathered statistic
__gc.gc_per_second__ |memStats.NumGC |#gc/second|number of gc's per second ( normalized)

 #### selfmon_device_stats

FieldName | Description
----------|------------
__snmp_oid_get_all__| All Gathered snmp metrics ( sum of snmpget oid's and all received oid's in snmpwalk queries)
__snmp_oid_get_processed__| Gathered and processed snmp metrics after filters are applied ( not always sent to the backend it depends on the report flag)
__snmp_oid_get_errors__| number of  oid with errors for all measurements
__cycle_gather_start_time__ | Last gathered time in unix timestamp
__cycle_gather_duration__ | elapsed time taken to get all measurement info in seconds
__filter_start_time__ | Last Applied Filter time  in unix timestamp
__filter_duration__ | elapsed time taken to compute all applicable filters on the device in seconds
__backend_sent_start_time__ | Last sent time to the internal output buffer
__backend_sent_Duration__ | elapsed time taken to send data to the internal output buffer backend
__metric_sent__| number of metrics sent (taken as fields) for all measurements
__metric_sent_errors__| number of metrics  (taken as fields) with errors for all measurements
__measurement_sent__| (number of  measurements build to send as a single request sent to the backend)
__measurement_sent_errors__| number of metrics  (taken as fields) with errors for all measurements

#### selfmon_outdb_stats

field | description
------|------------
__write_sent__ | number of HTTP writes sent to the DB (each write sends a batchPoint) on the last period
__write_error__ | number of HTTP write errors on the period
__points_sent__ | number of Points sent on each Write (on each BatchPoint) on the last period
__points_sent_max__ | max number of points sent on all writes on the last period
__points_sent_avg__ | (only if write_sent > 0) averaged points sent for all writes on the last period
__write_time__ | sum of all HTTP response times on  all writes on the last period
__write_time_max__ | max HTTP response time in all writes on the last period
__write_time_avg__ | (only if write_sent > 0) average response time for all writes on the last period
__fields_sent__ | number of fields sent to the DB on the last period
__fields_sent_max__ | max number of fields sent to the DB on the last period
__buffer_percent_used__| percent of the usage of the total buffer used for each.


Use this [GRAFANA SNMPCOLLECTOR DEVICE STATISTICS DASHBOARD](https://raw.githubusercontent.com/toni-moreno/snmpcollector/gh-pages/dashboards/GRAFANA_SNMPCOLLECTOR_DEVICESTATISTICS.json) to see your device stats and  this [GRAFANA SNMPCOLLECTOR GVM STATISTICS DASHBOARD ](https://raw.githubusercontent.com/toni-moreno/snmpcollector/gh-pages/dashboards/GRAFANA_SNMPCOLLECTOR_GVM.json) to see internal Golang Virtual Machine evolution