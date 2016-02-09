#Install Elasticsearch
Website: https://www.elastic.co
Forum: https://discuss.elastic.co/

##Install Java
brew install Caskroom/cask/java
`java -version`

```
java version "1.8.0_66"
Java(TM) SE Runtime Environment (build 1.8.0_66-b17)
Java HotSpot(TM) 64-Bit Server VM (build 25.66-b17, mixed mode)
```

##Install Elasticsearch

1. Download from: https://www.elastic.co/downloads/elasticsearch
1. Unzip the file: `elasticsearch-2.1.1`
1. Move the folder to a new directory: `/opt/elasticsearch-2.1.1`
1. Open the bin directory: `cd /opt/elasticsearch-2.1.1/bin`
1. Start the application: `./elasticsearch`
1. Verify the REST address from the terminal output

```
[2016-01-08 11:44:32,655][INFO ][node                     ] [Khaos] version[2.1.1], pid[2273], build[40e2c53/2015-12-15T13:05:55Z]
[2016-01-08 11:44:32,656][INFO ][node                     ] [Khaos] initializing ...
[2016-01-08 11:44:32,721][INFO ][plugins                  ] [Khaos] loaded [], sites []
[2016-01-08 11:44:32,748][INFO ][env                      ] [Khaos] using [1] data paths, mounts [[/ (/dev/disk0s2)]], net usable_space [238.2gb], net total_space [697.8gb], spins? [unknown], types [hfs]
[2016-01-08 11:44:34,249][INFO ][node                     ] [Khaos] initialized
[2016-01-08 11:44:34,249][INFO ][node                     ] [Khaos] starting ...
[2016-01-08 11:44:34,375][INFO ][transport                ] [Khaos] publish_address {127.0.0.1:9304}, bound_addresses {127.0.0.1:9304}, {[fe80::1]:9304}, {[::1]:9304}
...
[2016-01-08 11:44:38,925][INFO ][cluster.service          ] [Khaos] new_master {Khaos}{WsYFcaf-SDCWw2X7eJpPgQ}{127.0.0.1}{127.0.0.1:9304}, reason: zen-disco-join(elected_as_master, [0] joins received)
[2016-01-08 11:44:38,976][INFO ][http                     ] [Khaos] publish_address {127.0.0.1:9204}, bound_addresses {127.0.0.1:9204}, {[fe80::1]:9204}, {[::1]:9204}
[2016-01-08 11:44:38,977][INFO ][node                     ] [Khaos] started
[2016-01-08 11:44:38,983][INFO ][gateway                  ] [Khaos] recovered [0] indices into cluster_state
```

1. Open a web browser to the HTTP interface: `127.0.0.1:9204`

```
{
  "name" : "Khaos",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "2.1.1",
    "build_hash" : "40e2c53a6b6c2972b3d13846e450e66f4375bd71",
    "build_timestamp" : "2015-12-15T13:05:55Z",
    "build_snapshot" : false,
    "lucene_version" : "5.3.1"
  },
  "tagline" : "You Know, for Search"
}
```
 
##Install Kibana
###Mac OS
1. Download from: https://www.elastic.co/downloads/kibana
1. Unzip the file: `kibana-4.3.1-darwin-x64`
1. Move the folder to a new directory: `/opt/kibana-4.3.1-darwin-x64`
1. Open the bin directory: `cd /opt/kibana-4.3.1-darwin-x64`
1. Open `config/kibana.yml` in an editor
1. Edit the file to point Kibana to the Elasticsearch instance
```
# The Elasticsearch instance to use for all your queries.
elasticsearch.url: "http://localhost:9204"
```
1. Start Kibana `/opt/kibana-4.3.1-darwin-x64/bin/kibana`
1. Open a web browser to the URL in the log file: `http://0.0.0.0:5601` 


##Utilities
###Install elasticdump globally
1. Run the following:  
`sudo npm install elasticdump -g`

###Sense


##Download sample data
1. Download sample data  
https://github.com/guptayuvraj/Kibana_Essentials
1. Run the following  
```
elasticdump \
> --bulk=true \
> --input="/Users/brianmcmillan/Downloads/Kibana_Essentials-master/tweet.json" \
> --output=http://127.0.0.1:9200/
```

https://github.com/elastic/examples


###Install elasticdump globally
1. `sudo npm install elasticdump -g`
1. 

https://www.npmjs.com/package/elasticdump
https://www.npmjs.com/package/elasticsearch-tools



##Install Sense
https://www.elastic.co/guide/en/sense/current/installing.html

1. `./bin/kibana plugin --install elastic/sense`


##Install Timelion
https://www.elastic.co/blog/timelion-timeline
1. `./bin/kibana plugin -i kibana/timelion`