+++
author = "Anupam Kumar"
title = "Chromecast Connect"
date = "2022-07-05"
description = "The Chromecast Receiver and our first connection to Chromecast"
tags = [
	"gsoc",
	"gnome",
]
categories = [
	"gsoc",
]
aliases = []
image = "google-cast-logo.jpg"
+++

It has been a while studying and working with the Chromecast Protocol. This protocol is proprietary, and officially only the Chrome browser, Android, and iOS platforms support casting to a Chromecast-enabled device.
Now, Chromium is open source, and so is Android, but we would not want anything to do with Android for obvious reasons, not to mention this feature may be tucked away in Google Play Services.
So we settle with Chromium which is written in C++. Thankfully almost all of the hard work was done by many other open source projects, notably Node CastV2. Not only this project provides a simple implementation for the Chromecast protocol but also a written explanation of the underlying stuff and how things actually work.
All I need to figure out is how to implement that is C.

A brief overview of the connection and communication is as follows:
- We start by discovering the Chromecast devices using mDNS (Avahi) for devices named "googlecast"
- Next up we open a socket connection to the selected device on port 8009
- We then negotiate a TLS connection on the same socket connection
- Now comes the part where we open a "virtual connection" with the device
- With that done, the Chromecast device expects a "PING" message every 5 seconds to keep the virtual connection alive
- We are in the position to send and receive messages now

The messages are JSON encoded and serialized in protobuf according to the proto file conveniently found in the Chromium source code. Before sending the binary over, we prefix it with 4 bytes of packet length in Big Endian order. JSON is universal, good and easy to understand mostly because it is text, but what about protobuf? It is binary. Handy-dandy library protobuf-c save the day.

Here is what the proto file looks like:

```proto
// Copyright 2013 The Chromium Authors. All rights reserved.
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file.

syntax = "proto2";

option optimize_for = LITE_RUNTIME;

package castchannel;

message CastMessage {
  enum ProtocolVersion {
    CASTV2_1_0 = 0;
  }
  required ProtocolVersion protocol_version = 1;

  required string source_id = 2;
  required string destination_id = 3;

  required string namespace = 4;

  enum PayloadType {
    STRING = 0;
    BINARY = 1;
  }
  required PayloadType payload_type = 5;

  optional string payload_utf8 = 6;
  optional bytes payload_binary = 7;
}

message AuthChallenge {
}

message AuthResponse {
  required bytes signature = 1;
  required bytes client_auth_certificate = 2;
}

message AuthError {
  enum ErrorType {
    INTERNAL_ERROR = 0;
    NO_TLS = 1;
  }
  required ErrorType error_type = 1;
}

message DeviceAuthMessage {
  optional AuthChallenge challenge = 1;
  optional AuthResponse response = 2;
  optional AuthError error = 3;
}
```

We can send many types of messages like `CONNECT`, `PING`, `GET_STATUS`, `GET_APP_AVAILIBILITY`, `STOP`, `CLOSE`, etc.
These messages are sent in their respective namespaces (some of them are: `urn:x-cast:com.google.cast.tp.connection`, `urn:x-cast:com.google.cast.tp.heartbeat`, `urn:x-cast:com.google.cast.receiver`) along with some other values such as `sender_id`, `destination_id`, `payload_type`, `payload_utf8`, etc.
Different namespaces don't mean we create new channels for each one. They are just like the other values in the protobuf message.

We won't bother ourselves to verify the authenticity of the Chromecast device we are talking to. Thus all the Auth- messages shall not be entertained. We are much more interested in the `CastMessage`.
Protobuf-c comes with a lot of helper functions, but it offers yet more. We use `protoc`, "a code generator that converts Protocol Buffer .proto files to C descriptor code".

Having the TLS connection set up, we serialize a connect message and send it over.
For some odd reason when I sent `{ "type": "CONNECT" }`, it replied with `{ "type": "CLOSE" }`, replied `{ "type": "PING" }` with `{ "type": "PONG" }`, and `{ "type": "GET_STATUS" }` with `{ "type": "CLOSE" }`.
When I insisted on connection multiple times, it got mad and stopped replying to my messages :/
It still played ping pong though (replied to all the ping messages with pong).

We haven't come to terms since then. Was it because I left it hanging, and I have a feeling it must have waited for my messages all night?
I tried to sever all ties and make them all over again, shut it down and revived it, changed my address (local IP), and changed my name (sender_id), but who knows what is going inside that little mind of it.

Fear not our next mission involves peering into that mind and getting to the root cause of this confusion (Custom Web Receiver).

---

We didn't talk about the Custom Web Receiver this time and the protocols that would be implemented. Hang tight because that is what we are going to do in the coming days.

Many thanks to Benjamin and Claudio! I couldn't have made it without their help and advice.

---


Relevant Links:
- [Merge Request](https://gitlab.gnome.org/GNOME/gnome-network-displays/-/merge_requests/171)
- [Chromecast Docs](https://developers.google.com/cast/docs/)
- [Node CastV2](https://github.com/thibauts/node-castv2)
- [Chromium Source Code](https://chromium.googlesource.com/chromium/src.git)
- [Protobuf-c](https://github.com/protobuf-c/protobuf-c)
- [Cast Framework Messages](https://developers.google.com/cast/docs/reference/web_receiver/cast.framework.messages)

&nbsp;
