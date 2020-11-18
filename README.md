# Over-Simplified Messaging Library

_Ning Ren_

_Status: proposal_

Over-Simplified Messaging Library (OSML) is a modern real-time function-to-function messaging library.

It hides the concepts of networking, inter-process/intra-process comminucations, I/O asyncs, provide a **lightweight**, **universal** messaging interface.

## Over-Simplified Example

OSML is designed for distributed programs (microservices, server/mobile-app/webpage) fast-iterating or prototying.

Imagine not matter in what environment, at some point if you send a **message** to a **channel**, it should cost no more than one "receive" call to catch that message, anywhere.

```Python
def somewhere_in_my_program():
    # ...
    osml.send("#My-Secret-Channel-Name-Or-Token", "Hi there!")
```

The recipient could be an APP on mobile device, webpage in browser, microservice in container. It could be anywhere, e.g.:
1. Same process
2. Different process but same machine
3. Different machine but same LAN
4. Different local network but same Internet

As long as there are 'physical' paths, the library should be able to detect and build 'connection' via it.

```Javascript
osml.receive('#My-Secret-Channel-Name-Or-Token', function (msg) {
   console.log(msg);  // msg = "Hi there!"
});
```

## Just-in-time Path Detection

When you start an asynchronous application development, few things might come to your head.

* For multi-threading, you might think about message queue, shared memory, mutex, pipe/filesystem?
* For distributed, you might think about TCP/UDP/IP, HTTP, WebSockets, DMA.

There will be questions like: shall I set up message broker service (e.g. RabbitMQ, Redis Pub/Sub, Kafka)? Which IP/port shall it be? How can I connect my different components to it?

There are tons of options, and yes, if you picked the right one, you may get the optmial performance and setup for your application.

But for many people, they might want something simple to start with, leave the decision later. Fortunately. in ~90% cases, the selection can be done automatically by some heuristic or probing. E.g. sender/receiver in the same process can easily detect each other and set up a native message channel (using language/standard/3rd-party library).

Most of the time, people should be OK with the ~90% performance, completely forget about the implementation, and focus on their application logic. If they really want the last ~10%, OSML supports hint via channel.

```Python
osml.send("rpc://localhost:1234", "Hi there!")  # local OSML instance at :1234
osml.send("ws://localhost:1234", "Hi there!")  # local OSML instance at :1234
osml.send("https://osml.io/my-app-token", "Hi there!")  # Global OSML instance
osml.send("dma://192.168.0.10")
```

## Objective

OSML is a layer on top of {HTTP, WebSockets, etc.}, helping with choosing a right I/O implementaion based on just-in-time probing/heuristics.

OSML hides the complexity of underlying communications.

#### Goals

* Succinct API, e.g. Send/Receive, Pub/Sub
	* Channel is string, message is binary blob
* Works in all major languages
* Zero config to start
* Centralized service (e.g. osml.io), help with handshake, or as a message broker
	* Optional local OSML service, for best cluster, localhost performance
* Works without Internal access
* Best effort message delivery, but not guaranteed
* Hint particular implementation via channel
* An alternative to ZeroMQ/HTTP/WebSockets etc.

#### Non-Goals

* Not meant to replace TCP/IP, HTTP, WebSockets
* Not meant to replace ZeroMQ, RabbitMQ, Redis Pub/Sub, Kafka
* No serialization
* No message durability guaranteed (depends on impl)
* No message ordering guaranteed (depends on impl)
* No security feature

All these should be handled in application layer (or by some other libraries on OSML).
