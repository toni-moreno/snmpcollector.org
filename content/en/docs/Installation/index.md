---
title: "Installation"
linkTitle: "Installation"
weight: 3
description: >
  Detailed instructions to deploy a SnmpCollector instance
---

Head to and download the latest release and Install the suitable package  for your distribution

Debian | RedHat |Docker
-------|--------|------
[deb](http://snmpcollector-rel.s3.amazonaws.com/builds/snmpcollector_latest_amd64.deb) - [signature](http://snmpcollector-rel.s3.amazonaws.com/builds/snmpcollector_latest_amd64.deb.sha1)|[rpm](http://snmpcollector-rel.s3.amazonaws.com/builds/snmpcollector-latest-1.x86_64.rpm) - [signature](http://snmpcollector-rel.s3.amazonaws.com/builds/snmpcollector-latest-1.x86_64.rpm.sha1)| `docker run -d --name=snmpcol_instance00 -p 80:8090 -v /mylocal/conf:/opt/snmpcollector/conf -v /mylocal/log:/opt/snmpcollector/log  tonimoreno/snmpcollector`

Also downloadable rpm/deb/binary packages on the [releases](https://github.com/toni-moreno/snmpcollector/releases) site. You can see a list of features and changes in the [ChangeLog](https://github.com/toni-moreno/snmpcollector/blob/master/CHANGELOG.md). Check an feel free to change any of the predefined parameters in the main configuration placed in `/etc/snmpcollector/config.toml`

> Note: Environment variables SNMPCOL_XXXX for configuration parameters mapping are only available in 0.8.1+


> Note: upgrading docker from version < 0.8.1 to 0.8.1+ needs for UID/GID=472 ownership change for all persistent files/volumes otherwise you will see this message error "attempt to write a readonly database". refer to this issue #447


````toml
############################
# General Config
############################

[general]
 # InstanceID will be a string identifying the collector , It will be useful when
 # more than one running on the same server, this ID will be shown in the WebUI.
 # could be set also with SNMPCOL_GENERAL_INSTANCE_ID env var
 instanceID = "WAN Communicactions"

 # datadir set the directory where the data will be placed , also sqlite db if set as db engine
 # if not set the default datadir will be placed in the configuration directory
 # could be set also with SNMPCOL_GENERAL_DATA_DIR env var, default $CWD
 # datadir = "/var/lib/snmpcollector"

 # homedir set the directory where the public web dir will be placed
 # if not set the default homedir will be placed in the current directory
 # could be set also with SNMPCOL_GENERAL_HOME_DIR env var, default $CWD
 # homedir = "/var/lib/snmpcollector"

 # there are as many logs as devices configured
 # logdir set the Directory path for each device individual log
 # could be set also with SNMPCOL_GENERAL_LOG_DIR env var, default $CWD
 # logdir = "/var/log/somelogpath"

 # logLevel set the main process log level
 # valid values: panic,fatal,error,warn,info,debug
 # could also be set with SNMPCOL_GENERAL_LOG_LEVEL env var, default info if not set
 loglevel = "info"

 # Log mode  could be "file/console" if console have been selected
 # the content for the main snmpcollector.log and http_access.log will 
 # be writen in stdout 
 # could be set also with  SNMPCOL_GENERAL_LOG_MODE env var
 log_mode = "file"

 ############################
 # DataBase Config
 ############################

[database]
# type sets the sql backend
# valid values sqlite3,mysql,postgres
# could be set also with SNMPCOL_DATABASE_DRIVER_TYPE default sqlite3
 type = "sqlite3"

# host the host:port where doing DB connection (apply only to mysql/postgresql)
# could be set also with SNMPCOL_DATABASE_SERVER_HOST env var default localhost
# host = 127.0.0.1:3306

# user  Username to connect into DB (apply only to mysql/postgresql)
# could be set also with SNMPCOL_DATABASE_USERNAME env var
# user = "snmpcoluser"

# password Password to connect into DB (apply only to mysql/postgresql)
# could also be set with SNMPCOL_DATABASE_PASSWORD env var
# password = "snmpcolpass"

# name sets the database name
# could also be set with SNMPCOL_DATABASE_NAME  env var
 name = "snmpcollector"

# ssl_mode set ssl in database connection
# For "postgres", use either "disable", "require" or "verify-full"
# For "mysql", use either "true", "false", or "skip-verify".
# could also be set with SNMPCOL_DATABASE_SSL_MODE env var
# ssl_mode = disable

# client_cert_path path for client cert 
# could also be set with  SNMPCOL_DATABASE_CLIENT_CERT_PATH env var
# client_cert_path = /my/path/cert.pem

# client_key_path path for client cert key
# could also be set with  SNMPCOL_DATABASE_CLIENT_KEY_PATH env var
# client_key_path = /my/path/cert.key

# ca_cert_path path path for client cert key
# could also be set with  SNMPCOL_DATABASE_CA_CERT_PATH env var
# ca_cert_path= /my/path/ca.pem

# Log mode  could be "none/file/console" 
# if console have been selected all the SQL queries  will be writen into stdout 
# if file queries will be written in the logdir with name "sqllogfile" parameter if set or "sql.log" if not set 
# could be set also with  SNMPCOL_DATABASE_LOG_MODE env var
log_mode = "none"

# sqllogfile sets the name for a file in the log/ directory where sql backend will write all SQL transactions
# could also be set with SNMPCOL_DATABASE_SQL_LOG_FILE env var 
# sqllogfile = "mysql.log"

# debug adds extra verbosity to the SQL log
# could also be set with SNMPCOL_DATABASE_SQL_DEBUG env var
 debug = false



############################
# Self Monitorig Config
############################
#config for sending self monitoring metrics to our default influx db
# Sent Measurements will be <prefix>selfmon_gvm with the following fields
# runtime_goroutines
# *mem.alloc
# *mem.mallocs
# *mem.frees
# *gc.total_pause_ns
# *memory.heap
# *gc.pause_per_second
# *gc.pause_per_interval
# *gc.gc_per_second
# *gc.gc_per_interval

[selfmon]
 # enable true/false enable/disable self monitoring
 # could also be set with SNMPCOL_SELFMON_ENABLED env var
 enabled = true

 # send data Frequency
 # could also be set with SNMPCOL_SELFMON_FREQ env var
 freq = 60

 # prefix for measurement naming
 # could also be set with SNMPCOL_SELFMON_PREFIX env var
 prefix = ""
 
 # inherit device tags
 # could also be set with SNMPCOL_SELFMON_INHERIT_DEVICE_TAGS env var
 inheritdevicetags = true

 # adds extra tags to the measurement config should be set as a csv - tag=value1,tag2=value2,...,tagN=valN
 # could also be set with SNMPCOL_SELFMON_EXTRATAGS env va
 extratags = [ "instance=snmpcollector01" ]

############################
# Embedded WebServer Config
############################

[http]
 # listen where webserver will listen waiting for connections
 # could also be set with SNMPCOL_HTTP_LISTEN  env var
 listen   = ":8090"
 # NOTE: port = 8090 still usable option but DEPRECATED!!
 
 # Protocol could be set as https or https 
 # could also be set with SNMPCOL_HTTP_PROTOCOL  env var
 protocol = "http"

 # Cert key  Path to the certificate file (if protocol is set to https)
 # could also be set with SNMPCOL_HTTP_CERT_FILE  env var
 cert_file = "/path/to/certificate.pem"
 
 # Cert key  Path to the certificate key file (if protocol is set to https)
 # could also be set with SNMPCOL_HTTP_CERT_KEY  env var
 cert_key = "/path/to/certificate.key"

 # Admin user to log in the UI
 # could also be set with SNMPCOL_HTTP_ADMIN_USER  env var
 adminuser = "adm1"

 # Admin password to log in the UI
 # could also be set with SNMPCOL_HTTP_ADMIN_PASSWORD  env var
 adminpassword = "adm1pass"

 # When more than one instance you will need customize the cookie_id allowing navigate to all instances
 # could also be set with SNMPCOL_HTTP_COOKIE_ID  env var
 cookieid ="my_instance_cookie"

````

After that and if you are using sqlite3 config backend you  will be able to start an snmpcollector agent instance with a  zero configuration (without devices or metric definitions). It you prefer MySQL you must setup the database first

### MySQL Initial Setup 

After mysql install you will have to create  the database and configure user credentials over it you could use the following sql.

````sql
create database snmpcollector;
GRANT USAGE ON `snmpcollector`.* to 'snmpcoluser'@'localhost' identified by 'snmpcolpass';
GRANT ALL PRIVILEGES ON `snmpcollector`.* to 'snmpcoluser'@'localhost' with grant option;
flush privileges;
````

### PostgreSQL Initial Setup 

After postgresql install you will have to create  the database and configure user credentials over it you could use the following sql.
```
sudo -u postgres psql
postgres=# create database snmpcollector;
postgres=# create user snmpcoluser with encrypted password 'snmpcolpass';
postgres=# grant all privileges on database snmpcollector to snmpcoluser;
```

If you have any problems please open issue to the snmpcollector team

#  WebUI Navigation Features

## WebUI access.

### Login

Open the url http://your_snmpcollector_agent_host:8090/ 

{{< postimage "images/Login_BASE.JPG" "" >}}

You will be prompted to insert valid credentials.

Once logged you will see the main Runtime view with a list of devices and it's state.

{{< postimage "images/first_view_on_login.JPG" "" >}}

_snmpcollector has not yet support for different users with customized Access control , right now It has only one administrative user that can be customized into the [database] config.toml section with full access to all devices, configurations, runtime , console_

### Available components

Component | Description
----------|------------
[Variable Catalog](https://github.com/toni-moreno/snmpcollector/wiki/Component:-Variable-Catalog) | Add/edit/view Variables to use on STRINGEVAL metric type
[Influx Servers](https://github.com/toni-moreno/snmpcollector/wiki/Component:-InfluxDB-Servers) | Add/edit/view InfluxDB servers to store data
[OID Conditions](https://github.com/toni-moreno/snmpcollector/wiki/Component:-OID-Conditions) | Add/edit/view OID queries with some conditions to be applied on Metrics and Filters
[SNMP Metrics](https://github.com/toni-moreno/snmpcollector/wiki/Component:-SNMP-Metrics) | Add/edit/view Metrics you want to collect
[Measurements](https://github.com/toni-moreno/snmpcollector/wiki/Component:-Measurements) | Add/edit/view Measurements with selected defined Metrics
[Measurement Groups](https://github.com/toni-moreno/snmpcollector/wiki/Component:-Measurement-Groups) | Add/edit/view groups of Measurements to create easy configuration templates
[Measurement Filters](https://github.com/toni-moreno/snmpcollector/wiki/Component:-Measurement--Filters) | Add/edit/view Filters to be applied on Measurements and attached to the Device
[Custom Filters](https://github.com/toni-moreno/snmpcollector/wiki/Component:-Custom-Filters) | Add/edit/view filters based on a results query
[SNMP Devices](https://github.com/toni-moreno/snmpcollector/wiki/Component:-SNMP-Devices) | Add/edit/view desired Devices to collect data from

#### View

All components are listed with a table. The list view allows the user to add/modify/view/remove items stored on database.

- ![Edit Button](/webUI/Tables/edit_button.JPG) Edit Item: enters on modify view allowing to modify components parameters
- ![Export Button](/webUI/Tables/export_button.JPG) Export Item: Allows the user export selected item.
- ![View Button](/webUI/Tables/view_button.JPG) View Item: Allows the user to see all parameters of selected item
- ![New Button](/webUI/Tables/new_button.JPG) New Item: Allows the user enter into parameters form to create a new component
- ![Filter Input](/webUI/Tables/filter_input_button.JPG) Filter: Allows the user filter all columns. _Note_: it accepts regex expressions
- ![Remove Item](/webUI/Tables/remove_button.JPG) Remove Item: Allows the user  to remove selected item. When it is clicked it will show to the user elements that would be affected if the item is removed.
- ![Enable Edit](/webUI/Tables/enable_edit_button.jpg) Enable Edit: Allows the user to select different items and remove or change properties of them.
- ![Items per page](/webUI/Tables/items_per_page_button.JPG) Items per page: select the maximum items shown at same page



### Runtime component

Component | Description
----------|------------
[Runtime](/docs/runtime) | View what the agent is collecting and change options on runtime

### Data Management component

Component | Description
----------|------------
[Import](/docs/data-management/import)| Allows the user import data from file
[Export](/docs/data-management/export)| Allows the user export data from different components