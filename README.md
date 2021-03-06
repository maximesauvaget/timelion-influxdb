# InfluxDB plugin for Timelion

[Timelion](https://www.elastic.co/blog/timelion-timeline) (part of [Kibana](https://www.elastic.co/products/kibana)) provides a plugin mechanism by which you can create your own connectors to external datasets.

This plugin allows rendering data from the [InfluxDB](https://portal.influxdata.com/downloads) queries in Timelion, without having to duplicate timeseries into Elasticsearch.

![ezgif com-optimize 5](https://user-images.githubusercontent.com/1423657/43321404-08a22882-91ac-11e8-8eed-a961d71a0c65.gif)



## Installation instructions

#### Package
```
git clone https://github.com/lmangani/timelion-influxdb && cd timelion-influxdb
VERSION="6.2.4" ./release.sh
kibana-plugin install ./timelion-influxdb-1.0.0.zip
```

### Configuration
* All parameters including hostname can be defined inside the Timelion function
* *OPTIONAL* static InfluxDB parameters can be stored in `src/core_plugins/timelion/timelion.json`, e.g.
```
"influxdb": {
    "hostname": "my.influx.db",
    "port": 8086,
    "username": "username",
    "password": "password"
  },
```
* restart Kibana


## Usage (within Kibana's Timelion)
#### Parameters
![image](https://user-images.githubusercontent.com/1423657/43320300-560a576a-91a8-11e8-9ad7-45cc6993c254.png)

#### Examples:
##### Telegraf CPU
```
.influxdb(hostname='my.influx.db', db='telegraf', label='mem',metric='cpu', groupBy="1m", policy="autogen", field="usage_system")
```
##### Telegraf Memory w/ scaling
```
.influxdb(hostname='my.influx.db', db='telegraf', label='used',metric='mem', groupBy="1m", policy="autogen", field="used").cusum().derivative().mvavg(5).multiply(8).divide(1048576).lines(fill=2,width=1).color(#00FF00)
```
##### Telegraf Anomaly Detection
This simplified example is instpired by [rmoff](https://rmoff.net/2017/01/18/kibana-timelion-anomaly-detection/)
```
$thres=0.02, .influxdb(hostname='my.influx.db', db='telegraf', label='mem',metric='cpu', groupBy="1m", policy="autogen", field="usage_system").lines(1).if(eq, 0, null).holt(0.9, 0.1, 0.9, 0.5h).color(#eee).lines(10).label('Prediction'), .influxdb(hostname='my.influx.db', db='telegraf', label='mem',metric='cpu', groupBy="1m", policy="autogen", field="usage_system").color(#666).lines(1).label(Actual), .influxdb(hostname='my.influx.db', db='telegraf', label='mem',metric='cpu', groupBy="1m", policy="autogen", field="usage_system").lines(1).if(eq, 0, null).holt(0.9, 0.1, 0.9, 0.5h).subtract(.influxdb(hostname='my.influx.db', db='telegraf', label='mem',metric='cpu', groupBy="1m", policy="autogen", field="usage_system")).abs().if(lt, $thres, null, .influxdb(hostname='my.influx.db', db='telegraf', label='mem',metric='cpu', groupBy="1m", policy="autogen", field="usage_system")).points(10,3,0).color(#c66).label('Anomaly')
```
![image](https://user-images.githubusercontent.com/1423657/43539249-ab80b7b4-95c4-11e8-8c8d-e86ae95635d5.png)

## Versions

The plugin is intended for use with Kibana 5 and 6

If you are using a version of Kibana, you will need to edit kibana.version in the "package.json" file.


#### Other plugins that might be of interest

* [Prometheus](https://github.com/lmangani/timelion-prometheus) - query and display Prometheus data in Timelion
* [Random](https://github.com/rashidkpc/timelion-random) (by the author of Timelion) - A demo showing how to create a timelion plugin
* [USAFacts](https://github.com/rashidkpc/timelion-usafacts) (by the author of Timelion) - grabs series data from [usafacts.org](http://usafacts.org)
* [Google Analytics](https://github.com/bahaaldine/timelion-google-analytics) - brings Google Analytics data to Timelion
* [Mathlion](https://github.com/fermiumlabs/mathlion) (from Fermium Labs) - enables equation parsing and advanced maths

## Credits

The timelion-InfluxDB plugin is sponsored by [QXIP BV](http://qxip.net)

Elasticsearch and Kibana are trademarks of Elasticsearch BV, registered in the U.S. and in other countries.


