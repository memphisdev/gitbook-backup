---
description: This section describes Memphis consumer API
cover: ../../.gitbook/assets/Memphis concepts (2).jpeg
coverY: 0
---

# Consumer API

## What is a consumer?

A consumer is a client responsible for retrieving data or messages from the broker, particularly from the station. When a user configures a client connection to Memphis, it involves several components:

1. Connection - This represents an open socket connecting the client to Memphis, typically established when the client or application initializes for the first time.
2. Consumer - To read data or messages from Memphis, you need to declare a consumer entity.
3. (And/or) Producer - To write data or messages into Memphis, a producer entity declaration is required.

<figure><img src="../../.gitbook/assets/Producer.jpeg" alt=""><figcaption></figcaption></figure>

Memphis consumers are inherently designed for "long-polling," meaning they will patiently wait indefinitely until a new message is ingested into the Memphis station. This design includes built-in retry mechanisms for connection and polling, ensuring that consumers automatically attempt to reconnect in case of disconnection and repoll unacknowledged messages.

### Broker's Data Format

Memphis employs binary data encoding for reading, storing, and writing data to enhance performance, ensure format alignment, and optimize memory allocation. When a producer generates a message for a Memphis station, it undergoes conversion into a binary format.

An example from the `node.js` SDK using `.getData().toString()` -

```
consumer.on('message', (message) => {
  let msg = message.getData().toString();
  message.ack();
});
```

<figure><img src="../../.gitbook/assets/consume 1.jpeg" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
**Nonexistent** stations will be **automatically** generated by the SDK upon the initial connection of a producer or consumer.
{% endhint %}

### Parameters

(\*) Names might be a bit different from one SDK to another. Meanings are the same.

**Connection**

* `host`: Memphis URL
* `port`: Memphis port
* `username`: Can be root or any other application-type user
* `password`: Each application-type user comprises both a username and a password
* `connectionToken`: \*Valid only in case connection-token-based authentication was chosen\*\
  The token received when the user created. Will change in the future to more robust credentials and authentication system
* `reconnect`: The connection entity will try to reconnect to Memphis in case of a disconnection
* `maxReconnect`: Amount of time the client will try to reconnect before backing off
* `reconnectIntervalMs`: Time window between one retry to another
* `timeoutMs`: Ability to kill a dead connection after explicit time

**Consumer**

* `stationName`: The name of the station to be connected.
* `consumerName`: Consumer name.
* `consumerGroup`: Consumers are grouped under an object called "Consumer group." If not specified, a default CG will be created using the _consumerName._
* `pullIntervalMs`: Configured in milliseconds, this parameter defines the intervals of each consume operation. For example, if the value is set to 1000, it means that every 1000 ms, the consumer will try to pull new messages.
* `batchSize`: Defines how many messages will be collected per pull operation.
* `batchMaxTimeToWaitMs`: Defines how much time (in milliseconds) the consumer should wait for the entire required batch to be collected.
* `maxAckTimeMs`: For the consumer to receive the next message, the current one must be acknowledged, meaning the consumer is ready to consume and handle the next message. Oftentimes, the consumer gets crashed/throws an exception / not able to handle the message. The _`maxAckTimeMs`_ ensure that until X millisecond Memphis has not received ACK, it will automatically retransmit the message. If not configured correctly, it can result in duplicate processing.
* `maxMsgDeliveries`: The number of times Memphis will retransmit the same message to the same consumer. Max message deliveries.
* `caFile`: In case [encrypted client-Memphis](../../open-source-installation/kubernetes/) communication is used. '\<rootCA.pem>'.
* `certFile`: In case [encrypted client-Memphis](../../open-source-installation/kubernetes/) communication is used. '\<cert-client.pem>'.
* `keyFile`: In case [encrypted client-Memphis](../../open-source-installation/kubernetes/) communication is used. '\<key-client.pem>'.
* `prefetch = true`: will prefetch the next batch of messages and store it in memory for future Fetch() requests.

{% hint style="info" %}
For more information about how to create and connect a consumer to Memphis,&#x20;

please head [here](broken-reference)
{% endhint %}

## Sequence (Offsets)

The offset, in Memphis, is a straightforward integer that serves as a crucial reference point for tracking a consumer group's progress. It essentially acts as a bookmark, pinpointing the exact location of the last record dispatched to the consumer group in their most recent polling session. This means that Memphis ensures the consumer group doesn't receive duplicate records by consistently keeping tabs on their current offset.

Interestingly, Memphis distinguishes itself from most messaging systems by taking the responsibility of offset management away from the client and handling it as a coordinated effort between the broker and the SDK. This automatic offset handling mechanism ensures that acknowledged offsets are reliably recorded and maintained for the client, eliminating the need for manual offset tracking.

This unique feature allows the flexibility of revisiting and re-reading an acknowledged message if the need arises, offering an additional layer of control and precision in message processing within the Memphis framework.

## Prefetching

(Available for Go and Python SDKs)

The prefetching feature is a performance enhancement exclusive to the GO SDK, designed to boost throughput. When utilizing this optimization, the consumer primes the next round of fetches before presenting a set of records to the user through the consume() function. By doing so, it effectively overlaps the overhead of fetching data with the message processing phase.

While the consumer is actively processing the current batch of records, the broker can efficiently manage the consumer's ongoing fetch requests in the background. The primary objective is to ensure that data is readily available as soon as the consumer completes its processing cycle, invoking consume() again to receive the next batch of messages seamlessly. This prefetching mechanism optimizes the workflow, reducing potential latency and enhancing the overall efficiency of message retrieval and processing.

#### Fetch a single batch of messages

```
msgs, err := conn.FetchMessages("<station-name>", "<consumer-name>",
  memphis.FetchBatchSize(<int>) // defaults to 10
  memphis.FetchConsumerGroup("<consumer-group>"), // defaults to consumer name
  memphis.FetchBatchMaxWaitTime(<time.Duration>), // defaults to 5 seconds, has to be at least 1 ms
  memphis.FetchMaxAckTime(<time.Duration>), // defaults to 30 sec
  memphis.FetchMaxMsgDeliveries(<int>), // defaults to 10
  memphis.FetchConsumerGenUniqueSuffix(),
  memphis.FetchConsumerErrorHandler(func(*Consumer, error){})
  memphis.FetchStartConsumeFromSeq(<uint64>)// start consuming from a specific sequence. defaults to 1
  memphis.FetchLastMessages(<int64>)// consume the last N messages, defaults to -1 (all messages in the station))
```

#### Fetch a single batch of messages after consumer creation

`prefetch = true` will prefetch the next batch of messages and store it in memory for future Fetch() requests. Note: Use a higher `MaxAckTime` as the messages will reside in a local cache for some time before being processed.

```
msgs, err := consumer.Fetch(<batch-size> int, <prefetch> bool)
```

## Supported Protocols

* [NATS Protocol (Client SDKs)](broken-reference)
* [HTTP](https://github.com/memphisdev/memphis-http-proxy)
* [WebSockets](https://github.com/orgs/memphisdev/projects/2/views/1?pane=issue\&itemId=14008452) \* Soon \*
* gRPC \* Soon \*
* MQTT \* Soon \*
* Kafka \* Soon \*
* AMQP \* Soon \*

Search terms: max message deliveries, batch, batches
