# Aeron Stats Prometheus Exporter  

Prometheus exporter for [Aeron](https://github.com/real-logic/aeron) metrics.

Data is scraped by [prometheus](https://prometheus.io).


## Installation
```$xslt
./gradlew fatJar
```
## Running Locally

Firstly we will need to run a [aeron media driver](https://github.com/real-logic/aeron) first.

To run the stats exporter simply run the following while media driver is running. Delay is optional defaults to 1 second.
```$xslt
java -cp build/libs/aeron-prometheus-stats-all-1.0-SNAPSHOT.jar -Daeron.dir=<same_directory_as_media_driver> co.fairtide.prometheus_aeron_stat.PrometheusAeronStat port=1234 delay=<in_seconds>
```

### Docker:

The rabbitmq_exporter is sharing the network interface with the rabbitmq container -> it is possible to use localhost and default user/password (guest).

1.  Run a [aeron media driver](https://github.com/real-logic/aeron) first.


2. Build the docker image

        docker build -t prometheusaeron .
        
3. Run the docker image. Example configuration.

        PROMETHEUS_PORT=1234 DAERON_DIR=/dev/shm/aeron-asd DELAY_SECONDS=4   docker-compose -f docker-compose.yml up

Now your metrics are exposed through http://host:1234/metrics.

## Docker Configuration

Uses environment variables for configuration.
Settings:

Environment variable|default|description
--------------------|-------|------------
PROMETHEUS_PORT | No Default. Compulsory Argument| Port for prometheus to scrape.
DAERON_DIR | [DAERON_DIR](https://github.com/real-logic/aeron) | Check link for [details](https://github.com/real-logic/aeron)
DELAY_SECONDS | 1 second | Interval to query for stats. 


## Metrics

Two main type of matrics are exposed 

1. **System Counters**

Counters of significant events observed in the system such as errors, counts, rates, and hints that further investigation should be taken.
Aeron link -> [System counters](https://github.com/real-logic/aeron/wiki/Monitoring-and-Debugging) 

2. **Derived stats from Stream Positions**

num_channel - Number of channels that is going through media driver

num_subscribers - Number of subcribers on media driver

num_clients - Number of clients(subcriber and publisher) on media driver


### System Counters - Counter

The name of the stats almost directly come from aeron [link](https://github.com/real-logic/aeron/blob/master/aeron-driver/src/main/java/io/aeron/driver/status/SystemCounterDescriptor.java). It is renamed to suit prometheus conventions.

Command used to rename.
```$xslt
label.replaceAll(" ", "_").toLowerCase();
```

metric | description
-------| ------------
|bytes_sent | Total number of open channels|
|bytes_received | Total number of open connections|
|failed_offers_to_receiverProxy | Total number of message consumers|
|failed_offers_to_senderProxy | Total number of queues in use|
|failed_offers_to_driverconductorproxy | Total number of exchanges in use|
|naks_sent | Total number ready and unacknowledged messages in cluster.|
|naks_received | NAKs received.|
|status_messages_sent | Status Messages sent.|
|status_messages_received | Status Messages received|
|heartbeats_sent | Heartbeats sent|
|heartbeats_received | Heartbeats received|
|retransmits_sent | Retransmits sent|
|flow_control_under_runs | Flow control under runs|
|flow_control_over_runs | Flow control over runs.|
|invalid_packets | Invalid packets.|
|errors | Errors.|
|short_sends | Short sends|
|failed_attempts_to_free_log_buffers | Failed attempts to free log buffers|
|sender_flow_control_limits_applied | Sender flow control limits applied|
|unblocked_publications | Unblocked Publications.|
|unblocked_control_commands | Unblocked Control Commands.|
|possible_ttl_asymmetry | Possible TTL Asymmetry.|
|controllableidlestrategy_status | ControllableIdleStrategy status|
|Loss gap fills | Loss gap fills|

### Derived stats from Stream Positions - Gauge

Side note - running Arhive media driver will run a publisher on a channel.

metric | description
-------| ------------
|num_channel|number of channels in use|
|num_subscribers|received bytes|
|num_clients|received packets|


## Licence (See LICENSE file for full license)
Copyright 2018 Fairtide Pte. Ltd.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.