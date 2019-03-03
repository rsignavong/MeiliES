# MeiliES

MeiliES is an _Event Sourcing database_ that uses the **RESP** (REdis Serialization Protocol) to communicate.
This way it is possible to create clients by reusing the already available protocol.
For example it is possible to use [the official Redis command line interface] program to communicate with MeiliES.

## Building MeiliES

To run MeiliES you will need Rust, you can install it by following the steps on https://rustup.rs.
Once you have Rust in your `PATH` you can clone and build the MeiliES binaries.

```bash
git clone https://github.com/meilisearch/MeiliES.git
cd MeiliES
cargo install --path meilies-server
cargo install --path meilies-client
```

## Basic Event Store Usage

Once MeiliES is installed and available in your `PATH`, you can run it by executing the following command.

```bash
meilies-server
```

There is now a MeiliES server running on your machine and listening on `127.0.0.1:6480`.
In another terminal window you can specify to a client to listen to only new events.

```bash
meilies-client subscribe 'my-little-stream'
```

And in another one again you can send new events.
All clients which are subscribed to that same stream will see the events.

```bash
meilies-client publish 'my-little-stream' 'Hello World!'
meilies-client publish 'my-little-stream' 'Hello Cathy!'
meilies-client publish 'my-little-stream' 'Hello Kevin!'
meilies-client publish 'my-little-stream' 'Hello Donut!'
```

But that is not a really interresting usage of Event Sourcing, right?!
Lets do a more interresting usage of it.

## Real Event Store Usage

MeiliES stores all the events of all the streams that were sent by all the clients in the order they were received.

So lets check that and specify to one client the point in time where we want to start reading events.
Once there is no more events in the stream, the server start sending events at the moment it receives them.

We can do that by prepending the start event number separated by a colon.

```bash
meilies-client subscribe 'my-little-stream:0'
```

In this example we will start reading from the first event of the stream.
But we can also specify to start reading from the third event too.

```bash
meilies-client subscribe 'my-little-stream:2'
```

Or from events which does not exists yet!
Try sending events to this stream and you will see: only events from the fifth one will appear.

```bash
meilies-client subscribe 'my-little-stream:5'
```

[the official Redis command line interface]: https://redis.io/topics/rediscli
