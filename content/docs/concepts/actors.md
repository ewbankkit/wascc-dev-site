+++
title = "Actors"
linktitle = "Actors"
date = 2017-12-03

toc = true  # Show table of contents? true/false
type = "docs"  # Do not modify.
weight = 10

[menu.docs]
  parent = "concepts"
  weight = 10
+++

This document provides an overview of _actors_ as they apply to **wasCC** and WebAssembly.

# The Actor Model
The [actor model](https://en.wikipedia.org/wiki/Actor_model) has been around since **1979** and is not a new concept, though lately it seems to be enjoying something of a rennaissance. An _actor_ is the fundamental primitive of concurrent computation. 

Actors are _reactive_. In response to receiving a message, a traditional actor can: 

* make local decisions
* create more actors
* send more messages
* determine how to respond to the next message received

In the case of a **waSCC** actor, the only activity that actors are not allowed to perform is _create new actors_, which is a secure, privileged activity reserved for the host runtime.

# WebAssembly Actors
A subtle, yet vitally important benefit of the actor model is that all of the code that you execute within the confines of the actor is _single-threaded_. Actor developers do not have to worry about multi-threading, concurrency, or re-entrant functions.

The job of the actor developer is to simply define a response to each type of message in which the actor is interested. Actors are also completely and blissfully unaware of how their non-functional requirements are being met.

Actors do not know if there is an HTTP server present, or if there is a message broker present. If they are present, the actors do not know _which_ specific products or services are proving those capabilities.

In _waSCC_, an actor defines a single **receive function**, which the host runtime will invoke every time there is a new message targeted at that actor.

# Actors in Rust
In the [Rust actor SDK](https://github.com/wascc/wascc-actor), you indicate your receive function to the host runtime by using the `actor_receive!` macro:

```
actor_receive!(receive);
```

Then you simply define your receive function, which uses the _capabilities context_ to gain access to capabilities provided dynamically via the host runtime:

```rust
pub fn receive(ctx: &CapabilitiesContext, operation: &str, msg: &[u8]) -> CallResult {    
    match operation {
        http::OP_HANDLE_REQUEST => hello_world(ctx, msg),
        core::OP_HEALTH_REQUEST => Ok(vec![]),
        // ... handle other messages ... 
        _ => Err("bad dispatch".into()),
    }
}
```

Compiling this code into the `wasm32-unknown-unknown` target and signing it is then all that's necessary to build an actor. For more scenario-specific information on building actors, check out the [tutorials](/tutorials) section or the [GitHub examples](https://github.com/wascc/examples).