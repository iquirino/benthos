---
title: kafka_balanced
type: input
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the contents of:
     lib/input/kafka_balanced.go
-->


Connects to Kafka brokers and consumes topics by automatically sharing
partitions across other consumers of the same consumer group.


import Tabs from '@theme/Tabs';

<Tabs defaultValue="common" values={[
  { label: 'Common', value: 'common', },
  { label: 'Advanced', value: 'advanced', },
]}>

import TabItem from '@theme/TabItem';

<TabItem value="common">

```yaml
# Common config fields, showing default values
input:
  kafka_balanced:
    addresses:
    - localhost:9092
    topics:
    - benthos_stream
    client_id: benthos_kafka_input
    consumer_group: benthos_consumer_group
    batching:
      count: 1
      byte_size: 0
      period: ""
```

</TabItem>
<TabItem value="advanced">

```yaml
# All config fields, showing default values
input:
  kafka_balanced:
    addresses:
    - localhost:9092
    tls:
      enabled: false
      skip_cert_verify: false
      root_cas_file: ""
      client_certs: []
    sasl:
      mechanism: ""
      user: ""
      password: ""
      access_token: ""
      token_cache: ""
      token_key: ""
    topics:
    - benthos_stream
    client_id: benthos_kafka_input
    consumer_group: benthos_consumer_group
    start_from_oldest: true
    commit_period: 1s
    max_processing_period: 100ms
    group:
      session_timeout: 10s
      heartbeat_interval: 3s
      rebalance_timeout: 60s
    fetch_buffer_cap: 256
    target_version: 1.0.0
    batching:
      count: 1
      byte_size: 0
      period: ""
      condition:
        static: false
        type: static
      processors: []
```

</TabItem>
</Tabs>

Offsets are managed within Kafka as per the consumer group (set via config), and
partitions are automatically balanced across any members of the consumer group.

Partitions consumed by this input can be processed in parallel allowing it to
utilise <= N pipeline processing threads and parallel outputs where N is the
number of partitions allocated to this consumer.

The `batching` fields allow you to configure a
[batching policy](/docs/configuration/batching#batch-policy) which will be
applied per partition. Any other batching mechanism will stall with this input
due its sequential transaction model.

### Metadata

This input adds the following metadata fields to each message:

``` text
- kafka_key
- kafka_topic
- kafka_partition
- kafka_offset
- kafka_lag
- kafka_timestamp_unix
- All existing message headers (version 0.11+)
```

The field `kafka_lag` is the calculated difference between the high
water mark offset of the partition at the time of ingestion and the current
message offset.

You can access these metadata fields using
[function interpolation](/docs/configuration/interpolation#metadata).

## Fields

### `addresses`

A list of broker addresses to connect to. If an item of the list contains commas it will be expanded into multiple addresses.


Type: `array`  
Default: `["localhost:9092"]`  

```yaml
# Examples

addresses:
- localhost:9092

addresses:
- localhost:9041,localhost:9042

addresses:
- localhost:9041
- localhost:9042
```

### `tls`

Custom TLS settings can be used to override system defaults.


Type: `object`  
Default: `{"client_certs":[],"enabled":false,"root_cas_file":"","skip_cert_verify":false}`  

### `tls.enabled`

Whether custom TLS settings are enabled.


Type: `bool`  
Default: `false`  

### `tls.skip_cert_verify`

Whether to skip server side certificate verification.


Type: `bool`  
Default: `false`  

### `tls.root_cas_file`

The path of a root certificate authority file to use.


Type: `string`  
Default: `""`  

### `tls.client_certs`

A list of client certificates to use.


Type: `array`  
Default: `[]`  

```yaml
# Examples

client_certs:
- cert: foo
  key: bar

client_certs:
- cert_file: ./example.pem
  key_file: ./example.key
```

### `sasl`

Enables SASL authentication.


Type: `object`  
Default: `{"access_token":"","enabled":false,"mechanism":"","password":"","token_cache":"","token_key":"","user":""}`  

### `sasl.mechanism`

The SASL authentication mechanism, if left empty SASL authentication is not used. Warning: SCRAM based methods within Benthos have not received a security audit.


Type: `string`  
Default: `""`  
Options: `PLAIN`, `OAUTHBEARER`, `SCRAM-SHA-256`, `SCRAM-SHA-512`.

### `sasl.user`

A `PLAIN` username. It is recommended that you use environment variables to populate this field.


Type: `string`  
Default: `""`  

```yaml
# Examples

user: ${USER}
```

### `sasl.password`

A `PLAIN` password. It is recommended that you use environment variables to populate this field.


Type: `string`  
Default: `""`  

```yaml
# Examples

password: ${PASSWORD}
```

### `sasl.access_token`

A static `OAUTHBEARER` access token


Type: `string`  
Default: `""`  

### `sasl.token_cache`

Instead of using a static `access_token` allows you to query a [`cache`](/docs/components/caches/about) resource to fetch `OAUTHBEARER` tokens from


Type: `string`  
Default: `""`  

### `sasl.token_key`

Required when using a `token_cache`, the key to query the cache with for tokens.


Type: `string`  
Default: `""`  

### `topics`

A list of topics to consume from. If an item of the list contains commas it will be expanded into multiple topics.


Type: `array`  
Default: `["benthos_stream"]`  

### `client_id`

An identifier for the client connection.


Type: `string`  
Default: `"benthos_kafka_input"`  

### `consumer_group`

An identifier for the consumer group of the connection.


Type: `string`  
Default: `"benthos_consumer_group"`  

### `start_from_oldest`

If an offset is not found for a topic parition, determines whether to consume from the oldest available offset, otherwise messages are consumed from the latest offset.


Type: `bool`  
Default: `true`  

### `commit_period`

The period of time between each commit of the current partition offsets. Offsets are always committed during shutdown.


Type: `string`  
Default: `"1s"`  

### `max_processing_period`

A maximum estimate for the time taken to process a message, this is used for tuning consumer group synchronization.


Type: `string`  
Default: `"100ms"`  

### `group`

Tuning parameters for consumer group synchronization.


Type: `object`  
Default: `{"heartbeat_interval":"3s","rebalance_timeout":"60s","session_timeout":"10s"}`  

### `group.session_timeout`

A period after which a consumer of the group is kicked after no heartbeats.


Type: `string`  
Default: `"10s"`  

### `group.heartbeat_interval`

A period in which heartbeats should be sent out.


Type: `string`  
Default: `"3s"`  

### `group.rebalance_timeout`

A period after which rebalancing is abandoned if unresolved.


Type: `string`  
Default: `"60s"`  

### `fetch_buffer_cap`

The maximum number of unprocessed messages to fetch at a given time.


Type: `number`  
Default: `256`  

### `target_version`

The version of the Kafka protocol to use.


Type: `string`  
Default: `"1.0.0"`  

### `batching`

Allows you to configure a [batching policy](/docs/configuration/batching).


Type: `object`  
Default: `{"byte_size":0,"condition":{"static":false,"type":"static"},"count":1,"period":"","processors":[]}`  

```yaml
# Examples

batching:
  byte_size: 5000
  period: 1s

batching:
  count: 10
  period: 1s

batching:
  condition:
    text:
      arg: END BATCH
      operator: contains
  period: 1m
```

### `batching.count`

A number of messages at which the batch should be flushed. If `0` disables count based batching.


Type: `number`  
Default: `1`  

### `batching.byte_size`

An amount of bytes at which the batch should be flushed. If `0` disables size based batching.


Type: `number`  
Default: `0`  

### `batching.period`

A period in which an incomplete batch should be flushed regardless of its size.


Type: `string`  
Default: `""`  

```yaml
# Examples

period: 1s

period: 1m

period: 500ms
```

### `batching.condition`

A [condition](/docs/components/conditions/about) to test against each message entering the batch, if this condition resolves to `true` then the batch is flushed.


Type: `object`  
Default: `{"static":false,"type":"static"}`  

### `batching.processors`

A list of [processors](/docs/components/processors/about) to apply to a batch as it is flushed. This allows you to aggregate and archive the batch however you see fit. Please note that all resulting messages are flushed as a single batch, therefore splitting the batch into smaller batches using these processors is a no-op.


Type: `array`  
Default: `[]`  

```yaml
# Examples

processors:
- archive:
    format: lines

processors:
- archive:
    format: json_array

processors:
- merge_json: {}
```

