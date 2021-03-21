From this page you will be able to download a definition and a dashboard from some popular devices/products.

## Import Instructions

### SnmpCollector Template

1.-Download the json file and Import it, after you will see a new device with id="localhost"
2.- Change the ID , host , port to define your linux box device, and test connectivity with the snmp connection tool
3.- Change the imported InfluxDB server to any of your previously defined influx servers

### Grafana Template

1.- Download the json file and import it. Make sure you have been created the influx datasource before.

## Template List

|Product| SnmpCollector Template | Grafana Template | Detailed Description |
|-------|------------------------|------------------|----------------------|
|Linux based Devices|[Linux SnmpCollector Template](https://raw.githubusercontent.com/toni-moreno/snmpcollector/gh-pages/templates/linux_based_device/snmpcollector_LINUX_BASED_DEVICE_20170712.json) | [Linux Grafana Template](https://raw.githubusercontent.com/toni-moreno/snmpcollector/gh-pages/templates/linux_based_device/Grafana_LINUX%20BASED_DEVICE-20170712.jsonhttps://raw.githubusercontent.com/toni-moreno/snmpcollector/gh-pages/templates/linux_based_device/Grafana_LINUX%20BASED_DEVICE-20170712.json)| This template will get cpu/memory/disk/fs/netio metrics from any Linux based devices with a net-snmp agent running on it, most linux based appliances will work with these templates ![linux](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/templates/linux_based_device/linux_based_devices_screenshot.JPG?raw=true)|

 