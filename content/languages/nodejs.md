---
title: NodeJS
weight: 4
---

<table>
<tr><td>Github</td><td>https://github.com/zeromq/zeromq.js/</td></tr>
<tr><td>npm</td><td>https://www.npmjs.com/package/zeromq</td></tr>
</table>

## Installation

To install ZeroMQ for Node, download and install the package from npm. Here we use the version 5.x branch since version 6.x is still in beta.

```
$ npm install zeromq@5
```

## Examples using zeromq

### Push/Pull

This example demonstrates how a producer pushes information onto a
socket and how a worker pulls information from the socket.

**producer.js**

```js
// producer.js
const zmq = require("zeromq")

async function run() {
  const sock = new zmq.Push

  await sock.bind("tcp://127.0.0.1:3000")
  console.log("Producer bound to port 3000")

  while (true) {
    await sock.send("some work")
    await new Promise(resolve => setTimeout(resolve, 500))
  }
}

run()
```

**worker.js**

```js
// worker.js
const zmq = require("zeromq")

async function run() {
  const sock = new zmq.Pull

  sock.connect("tcp://127.0.0.1:3000")
  console.log("Worker connected to port 3000")

  for await (const [msg] of sock) {
    console.log("work: %s", msg.toString())
  }
}

run()
```

### Pub/Sub

This example demonstrates using `zeromq` in a classic Pub/Sub,
Publisher/Subscriber, application.

**Publisher: publisher.js.js**

```js
// publisher.js.js
const zmq = require("zeromq")

async function run() {
  const sock = new zmq.Publisher

  await sock.bind("tcp://127.0.0.1:3000")
  console.log("Publisher bound to port 3000")

  while (true) {
    console.log("sending a multipart message envelope")
    await sock.send(["kitty cats", "meow!"])
    await new Promise(resolve => setTimeout(resolve, 500))
  }
}

run()
```

**Subscriber: subscriber.js.js**

```js
// subscriber.js.js
const zmq = require("zeromq")

async function run() {
  const sock = new zmq.Subscriber

  sock.connect("tcp://127.0.0.1:3000")
  sock.subscribe("kitty cats")
  console.log("Subscriber connected to port 3000")

  for await (const [topic, msg] of sock) {
    console.log("received a message related to:", topic, "containing message:", msg)
  }
}

run()
```
