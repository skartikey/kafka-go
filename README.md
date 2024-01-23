# Kafka-Go

This repository contains Go client applications designed for producing and consuming messages from an Apache Kafka® cluster.

## Kafka Setup

For our client application to operate effectively, we require a Kafka Cluster. In this guide, we will set up Kafka locally in Docker using the Confluent CLI. Note that this example is based on a Linux machine; however, alternative methods can be found on the Confluent website.

```bash
curl -sL --http1.1 https://cnfl.io/cli | sh -s -- latest
export PATH=$(pwd)/bin:$PATH
```

Now, initiate the Kafka broker:

```bash
confluent local kafka start
```

Take note of the Plaintext Ports displayed in your terminal, as you will need them when configuring the client in the next step.

### Create Topic

A topic in Kafka represents an immutable, append-only log of events. Typically, a topic is composed of similar kinds of events. In this guide, we create a topic for retail purchases.

Create a new topic named "purchases," which you will use for both producing and consuming events.

```bash
confluent local kafka topic create purchases
```

### Compile Producer and Consumer

Compile the producer using the following command:

```bash
go build -o out/producer util.go producer.go
```

Compile the consumer as follows:

```bash
go build -o out/consumer util.go consumer.go
```

### Produce Events

To run the producer, execute the compiled binary and pass in the configuration file created above:

```bash
./out/producer kafka.properties
```

You should observe output resembling the following:

```
Produced event to topic purchases: key = awalther   value = t-shirts
Produced event to topic purchases: key = awalther   value = batteries
Produced event to topic purchases: key = jsmith     value = gift card
Produced event to topic purchases: key = jsmith     value = book
Produced event to topic purchases: key = htanaka    value = book
Produced event to topic purchases: key = sgarcia    value = alarm clock
Produced event to topic purchases: key = eabara     value = batteries
Produced event to topic purchases: key = htanaka    value = batteries
Produced event to topic purchases: key = jbernard   value = book
Produced event to topic purchases: key = awalther   value = alarm clock
```

### Consume Events

In another terminal, run the following command to execute the consumer application, which reads events from the "purchases" topic and writes the information to the terminal.

```bash
./out/consumer kafka.properties
```

The consumer application will start, print any unconsumed events, and then wait for more events to arrive. On startup, you should see output resembling this:

```
Consumed event from topic purchases: key = jsmith     value = alarm clock
Consumed event from topic purchases: key = htanaka    value = book
Consumed event from topic purchases: key = eabara     value = batteries
Consumed event from topic purchases: key = htanaka    value = t-shirts
Consumed event from topic purchases: key = htanaka    value = t-shirts
Consumed event from topic purchases: key = htanaka    value = gift card
Consumed event from topic purchases: key = sgarcia    value = gift card
Consumed event from topic purchases: key = jbernard   value = gift card
Consumed event from topic purchases: key = awalther   value = alarm clock
Consumed event from topic purchases: key = htanaka    value = book
```

When you are finished with the consumer, press Ctrl-C to terminate the consumer application.

Finally, shut down Kafka when you are done using the following command:

```bash
confluent local kafka stop
```