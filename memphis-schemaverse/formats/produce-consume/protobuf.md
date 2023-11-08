# Protobuf

[Protocol Buffers (Protobuf)](https://developers.google.com/protocol-buffers) is a free and open-source cross-platform data format used to serialize structured data, Initially released on July 7, 2008. It is useful in developing programs to communicate with each other over a network or for storing data. The method involves an interface description language that describes the structure of some data and a program that generates source code from that description for generating or parsing a stream of bytes that represents the structured data.

### Supported versions

* proto2
* proto3

### Supported Features

* Retrieve compiled protobuf schemas (Produce messages without .proto files)
* Versioning
* Embedded serialization
* Live evolution
* Import packages (soon)
* Import types (soon)

## Getting started

### How to produce a message

{% tabs %}
{% tab title="Node.js" %}
Memphis abstracts the need for external serialization functions and embeds them within the SDK.

In node.js, we can simply produce an object. Behind the scenes, the object will be serialized based on the attached schema and data format - protobuf.

**Example schema: (No need to compile)**

```protobuf
syntax = "proto3";
message Test {
            string field1 = 1;
            string  field2 = 2;
            int32  field3 = 3;
}
```

**Producing a message **<mark style="color:purple;">**without**</mark>** a local .proto file:**

{% code lineNumbers="true" %}
```javascript
const { memphis } = require("memphis-dev");

(async function () {
    let memphisConnection

    try {
        memphisConnection = await memphis.connect({
            host: "MEMPHIS_BROKER_URL",
            username: "APPLICATION_USER",
            password: "PASSWORD",
            accountId: ACCOUNT_ID //*optional* In case you are using Memphis.dev cloud
        });
        const producer = await memphisConnection.producer({
            stationName: "STATION_NAME",
            producerName: "PRODUCER_NAME"
        });
        var payload = {
            field1: "AwesomeString",
            field2: "AwesomeString",
            field3: 54
        };
        await producer.produce({
            message: payload
        })
        memphisConnection.close();

    } catch (ex) {
        console.log(ex);
        if (memphisConnection) memphisConnection.close();
    }
})();
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
Memphis abstracts the need for external serialization functions and embeds them within the SDK.\
\
**Example proto file:**

```protobuf
syntax = "proto3";
option go_package = "./";

message Test {
            string field1 = 1;
            string field2 = 2;
            int32 field3 = 3;
}
```

To compile the proto file, run the following command:&#x20;

```bash
protoc --go_out=. ./{proto file name}
```

**Producing a message **<mark style="color:purple;">**without**</mark>** a local .proto file:**

```go
package main

import (
    "fmt"
    "os"
    "github.com/memphisdev/memphis.go"
)

func main() {
    conn, err := memphis.Connect(
        "MEMPHIS_BROKER_URL", 
        "APPLICATION_TYPE_USERNAME", 
        memphis.Password("PASSWORD"),
        memphis.AccountId(123456789), //*optional* In case you are using Memphis.dev cloud
        )
    if err != nil {
        os.Exit(1)
    }
    defer conn.Close()
    p, err := conn.CreateProducer("STATION_NAME", "PRODUCER_NAME")

    hdrs := memphis.Headers{}
    hdrs.New()
    err = hdrs.Add("key", "value")

    if err != nil {
        fmt.Printf("Header failed: %v\n", err)
        os.Exit(1)
    }
	msg := make(map[string]interface{})
	msg["field1"] = "value1"
	msg["field2"] = "value2"
	msg["field3"] = 32

    err = p.Produce(msg, memphis.MsgHeaders(hdrs))

    if err != nil {
        fmt.Printf("Produce failed: %v\n", err)
        os.Exit(1)
    }
}

```

**Producing a message **<mark style="color:purple;">**with**</mark>** a local .proto file:**

<pre class="language-go"><code class="lang-go">package main

import (
    "fmt"
    "os"
    "demo/schemapb"
    "github.com/memphisdev/memphis.go"
)

func main() {
<strong>    conn, err := memphis.Connect(
</strong>        "MEMPHIS_BROKER_URL", 
        "APPLICATION_TYPE_USERNAME", 
        memphis.Password("PASSWORD"),
        memphis.AccountId(123456789), //*optional* In case you are using Memphis.dev cloud
        )
    if err != nil {
        os.Exit(1)
    }
    defer conn.Close()
    p, err := conn.CreateProducer("STATION_NAME", "PRODUCER_NAME")

    hdrs := memphis.Headers{}
    hdrs.New()
    err = hdrs.Add("key", "value")
    if err != nil {
        fmt.Printf("Header failed: %v\n", err)
        os.Exit(1)
    }
    s1 := "Hello"
    s2 := "World"
    pbInstance := schemapb.Test{
	Field1: s1,
	Field2: s2,
    }

    err = p.Produce(&#x26;pbInstance, memphis.MsgHeaders(hdrs))
    if err != nil {
        fmt.Printf("Produce failed: %v\n", err)
        os.Exit(1)
    }
}
        
</code></pre>
{% endtab %}

{% tab title="Python" %}
Memphis abstracts the need for external serialization functions and embeds them within the SDK.

**Example schema:**

```protobuf
syntax = "proto3";
message Test {
            string field1 = 1;
            string field2 = 2;
            int32 field3 = 3;
}
```

To compile the proto file, run the following command:&#x20;

```bash
protoc --go_out=. ./{proto file name}
```

**Producing a message **<mark style="color:purple;">**with**</mark>** a local .proto file:**

```python
import asyncio
from memphis import Memphis, Headers, MemphisError, MemphisConnectError, MemphisSchemaError

import schema_pb2 as PB

async def main():
    memphis = Memphis()
    await memphis.connect(host="MEMPHIS_BROKER_URL", username="APPLICATION_TYPE_USERNAME", password="PASSWORD", account_id=ACCOUNT_ID)
    producer = await memphis.producer(
        station_name="STATION_NAME", producer_name="PRODUCER_NAME")

    headers = Headers()
    headers.add("key", "value")

    obj = PB.Test()
    obj.field1 = "Hello"
    obj.field2 = "Amazing"
    obj.field3 = 32
    
    try:
        await producer.produce(obj, headers=headers)

    except Exception as e:
        print(e)
    finally:
        await asyncio.sleep(3)

    await memphis.close()

if __name__ == '__main__':
    asyncio.run(main())
```
{% endtab %}

{% tab title="TypeScript" %}
Memphis abstracts the need for external serialization functions and embeds them within the SDK.

**Example schema:**

```protobuf
syntax = "proto3";
message Test {
            string field1 = 1;
            string field2 = 2;
            int32 field3 = 3;
}
```

**Producing a message **<mark style="color:purple;">**without**</mark>** a local .proto file:**

```typescript
import { memphis, Memphis } from 'memphis-dev';

(async function () {
    let memphisConnection: Memphis;

    try {
        memphisConnection = await memphis.connect({
            host: 'MEMPHIS_BROKER_URL',
            username: 'APPLICATION_TYPE_USERNAME',
            password: 'PASSWORD',
            accountId: ACCOUNT_ID //*optional* In case you are using Memphis.dev cloud
        });

        const producer = await memphisConnection.producer({
            stationName: 'STATION_NAME',
            producerName: 'PRODUCER_NAME'
        });

        const headers = memphis.headers()
        headers.add('key', 'value');
        const msg = {
            field1: "Hello",
            field2: "Amazing",
            field3: 32
        }
        await producer.produce({
            message: msg,
            headers: headers
        });

        memphisConnection.close();
    } catch (ex) {
        console.log(ex);
    }
})();
```
{% endtab %}

{% tab title=".NET" %}
Memphis abstracts the need for external serialization functions and embeds them within the SDK.

**Example schema:**

{% code overflow="wrap" lineNumbers="true" %}
```protobuf
syntax = "proto3";
message Test {
            string field1 = 1;
            string field2 = 2;
            int32 field3 = 3;
}
```
{% endcode %}

**Producing a message **<mark style="color:purple;">**without**</mark>** a local .proto file:**

{% code overflow="wrap" lineNumbers="true" %}
```aspnet
using Memphis.Client.Producer;
using System.Collections.Specialized;
using Memphis.Client;
using ProtoBuf;

var options = MemphisClientFactory.GetDefaultOptions();
options.Host = "<memphis-host>";
options.Username = "<application type username>";
options.ConnectionToken = "<broker-token>";

/**
* In case you are using Memphis.dev cloud
* options.AccountId = "<account-id>";
*/

try
{
    var client = await MemphisClientFactory.CreateClient(options);

    var producer = await client.CreateProducer(new MemphisProducerOptions
    {
        StationName = "<memphis-station-name>",
        ProducerName = "<memphis-prodcducer-name>",
        GenerateUniqueSuffix = true
    });

    NameValueCollection commonHeaders = new()
    {
        {
            "key-1", "value-1"
        }
    };

    Test test = new()
    {
        Field1 = "Hello",
        Field2 = "Amazing",
        Field3 = 32
    };
    using var memoryStream = new MemoryStream();
    Serializer.Serialize(memoryStream, test);
    var message = memoryStream.ToArray();

    await producer.ProduceAsync(message, commonHeaders);
    client.Dispose();
}
catch (Exception exception)
{
    Console.WriteLine($"Error occured: {exception.Message}");
}

[ProtoContract]
class Test
{
    [ProtoMember(1, Name = "field1")]
    public required string Field1 { get; set; }
    [ProtoMember(2, Name = "field2")]
    public required string Field2 { get; set; }
    [ProtoMember(3, Name = "field3")]
    public int Field3 { get; set; }
}
```
{% endcode %}
{% endtab %}

{% tab title="REST" %}
In REST, you can simply produce an object. Behind the scenes, the object will be serialized based on the attached schema and data format - protobuf.

**Example schema:**

```protobuf
syntax = "proto3";
message Test {
            string field1 = 1;
            string field2 = 2;
            int32 field3 = 3;
}
```

**Producing a message **<mark style="color:purple;">**without**</mark>** a local .proto file:**

{% code lineNumbers="true" %}
```javascript
var axios = require('axios');
var data = JSON.stringify({
  "field1": "foo",
  "field2": "bar",
  "field3": 123,
});

var config = {
  method: 'post',
  url: 'https://BROKER_RESTGW_URL/stations/hps/produce/single',
  headers: { 
    'Authorization': 'Bearer <jwt>', 
    'Content-Type': 'application/json'
  },
  data : data
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});
```
{% endcode %}
{% endtab %}
{% endtabs %}

### How to consume a message (Deserialization)

{% tabs %}
{% tab title="Node.js" %}
{% code lineNumbers="true" %}
```javascript
const { memphis } = require("memphis-dev");

(async function () {
    try {
        await memphis.connect({
            host: "localhost",
            username: "CLIENT_TYPE_USERNAME",
            password: "PASSWORD"
            accountId: ACCOUNT_ID //*optional* In case you are using Memphis.dev cloud
        });

        const consumer = await memphis.consumer({
            stationName: "marketing",
            consumerName: "cons1",
            consumerGroup: "cg_cons1",
            maxMsgDeliveries: 3,
            maxAckTimeMs: 2000,
            genUniqueSuffix: true
        });

        consumer.on("message", message => {
            console.log(message.getDataDeserialized());
            message.ack();
        });
        consumer.on("error", error => {
            console.log(error);
        });
    } catch (ex) {
        console.log(ex);
        memphis.close();
    }
})();
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
```go
package main

import (
	"fmt"
	"os"
	"time"

	"github.com/memphisdev/memphis.go"
)

func main() {
	conn, err := memphis.Connect(
        	"MEMPHIS_BROKER_URL", 
	        "APPLICATION_TYPE_USERNAME", 
	        memphis.Password("PASSWORD"),
	        memphis.AccountId(123456789), //*optional* In case you are using Memphis.dev cloud
        )
	if err != nil {
		os.Exit(1)
	}
	defer conn.Close()

	consumer, err := conn.CreateConsumer("CONSUMER_NAME", "CONSUMER_GROUP_NAME")
	if err != nil {
		fmt.Printf("Consumer creation failed: %v\n", err)
		os.Exit(1)
	}

	handler := func(msgs []*memphis.Msg, err error) {
		if err != nil {
			fmt.Printf("Fetch failed: %v\n", err)
			return
		}

		for _, msg := range msgs {
			fmt.Println(string(msg.DataDeserialized()))	
			msg.Ack()
		}
	}

	consumer.Consume(handler)
	time.Sleep(3000 * time.Second)
}
```
{% endtab %}

{% tab title="Python" %}
```python
import asyncio
from memphis import Memphis

async def main():
    async def msg_handler(msgs, error):
        try:
            for msg in msgs:
                print("message: ", await msg.get_data_deserialized())
                await msg.ack()
            if error:
                print(error)
        except Exception as e:
            print(e)

    try:
        memphis = Memphis()
        await memphis.connect(host="MEMPHIS_HOST", username="MEMPHIS_USERNAME", password="PASSWORD", account_id=ACCOUNT_ID)
        consumer = await memphis.consumer(
            station_name="STATION_NAME", consumer_name="CONSUMER_NAME")
        consumer.consume(msg_handler)
        # Keep your main thread alive so the consumer will keep receiving data
        await asyncio.Event().wait()
    except Exception as e:
        print(e)
    finally:
        await memphis.close()

if __name__ == '__main__':
    asyncio.run(main())
```
{% endtab %}

{% tab title="TypeScript" %}
```typescript
import { memphis, Memphis } from 'memphis-dev';

(async function () {
    let memphisConnection: Memphis;
    try {
        memphisConnection = await memphis.connect({
            host: 'MEMPHIS_BROKER_URL',
            username: 'APPLICATION_TYPE_USERNAME',
            password: 'PASSWORD',
            accountId: ACCOUNT_ID //*optional* In case you are using Memphis.dev cloud
        });

        const consumer = await memphis.consumer({
            stationName: "STATION_NAME",
            consumerName: "CONSUMER_NAME",
            consumerGroup: "CONSUMER_GROUP_NAME",
            maxMsgDeliveries: 3,
            maxAckTimeMs: 2000,
            genUniqueSuffix: true
        });

        consumer.on("message", message => {
            console.log(message.getDataDeserialized());
            message.ack();
        });
        consumer.on("error", error => {
            console.log(error);
        });
    } catch (ex) {
        console.log(ex);
        memphis.close();
    }
})();type
```
{% endtab %}

{% tab title=".NET" %}
**Example schema:**

{% code overflow="wrap" lineNumbers="true" %}
```protobuf
syntax = "proto3";
message Test {
            string field1 = 1;
            string field2 = 2;
            int32 field3 = 3;
}
```
{% endcode %}

**Consumption**

{% code overflow="wrap" lineNumbers="true" %}
```aspnet
using Memphis.Client.Consumer;
using Memphis.Client;

var options = MemphisClientFactory.GetDefaultOptions();
options.Host = "<memphis-host>";
options.Username = "<application type username>";
options.ConnectionToken = "<broker-token>";

/**
* In case you are using Memphis.dev cloud
* options.AccountId = "<account-id>";
*/

try
{
    var client = await MemphisClientFactory.CreateClient(options);

    var consumer = await client.CreateConsumer(new MemphisConsumerOptions
    {
        StationName = "<station-name>",
        ConsumerName = "<consumer-name>",
        ConsumerGroup = "<consumer-group-name>",
    });

    consumer.MessageReceived += (sender, args) =>
    {
        if (args.Exception is not null)
        {
            Console.Error.WriteLine(args.Exception);
            return;
        }

        foreach (var msg in args.MessageList)
        {
            Console.WriteLine($"Received data: {msg.GetDeserializedData()}");
        }
    };

    consumer.ConsumeAsync();

    await Task.Delay(TimeSpan.FromMinutes(1));
    await consumer.DestroyAsync();
    client.Dispose();
}
catch (Exception exception)
{
    Console.WriteLine($"Error occured: {exception.Message}");
}
```
{% endcode %}
{% endtab %}
{% endtabs %}
