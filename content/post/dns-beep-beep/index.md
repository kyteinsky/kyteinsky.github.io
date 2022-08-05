+++
author = "Anupam Kumar"
title = "DNS Based Communication"
date = "2022-08-05"
description = "Communication with a remote server only through DNS protocol"
tags = [ "dns", "go" ]
categories = [ "recreational" ]
aliases = []
image = "tunnel.jpeg"
+++

> This project is about a DNS server that can handle communications (one way).

## Brief

So what do I mean when I say it can handle communications? It means that you can send messages to a remote server, which happens to be an authoritative DNS server, in plain text as well as encrypted data.

The idea mainly stems from a Youtube video (whose link seems to have been lost in the internet haystack).
The person in the video explains its use case when you have a network with HTTP(S) ports blocked, some special service using some custom port, and the default DNS port 53 unblocked (maybe they forgot, maybe they use it for some reason).
This is a particular and unlikely situation, but the problem at hand is rather interesting. We need to make a DNS-based one-way message transfer protocol for fun.

## Plan

The exact action plan would be decided upon further inspection of the DNS protocol, its capabilities and limitations. For now, we won't bother ourselves with the fine details.

Without looking into the DNS specifications, here's a crude plan of how things should go:

The first step would be to create a DNS server that has access to the DNS query and gets some say in the response (yeah, some libraries can be used for _development ease_, and writing a response could be redundant).
Now that reading and writing are possible, it is time to define the protocol structure in Unga Bunga.

Usually, the query we send in a DNS request is a domain name structured in the form `abcd.efgh.com`.
We can use the actual domain name and sub-domain names to act as our payload here (allowed characters are 26 alphabet characters in lowercase, 10 digits, and 1 hyphen)
- as plain text
- as encrypted text

#### Plain text
We can use hyphens to replace blank spaces and an encoding method as `0-` followed by the word to be capitalized in this manner: `hello-0-world`. This would mean `hello World`, and as a general rule of thumb, we could just make the first letter capital when such encoding was being used, giving us the sweet output `Hello World`.

#### Encrypted text
We discuss encoded here too since there is not much difference between them once the plain text has been processed.
We have limitless power here. Just encrypt the text and use a total of 26+10+1 characters to pass it on, or just use the *here* less efficient hex characters directly obtained from the encoder or encryptor.

---

But wait, the length of the domain is limited! How do we send a longer message?
Here comes the `com` part of the domain into the picture. It can specify if we have more content waiting to be sent or if we are finished.
Let us, for now, reserve the first character to check if we have more input to read: `0` for not finished yet and `1` for done.
And the following four characters for the sequence number. This shall start at `1` for the first partial message and go on increasing for the following messages. This ensures we assemble the whole message in the correct order in case they are not received in the same order as sent.

For the response part, we can follow in TCP's footsteps. We respond to all the queries with the sequence numbers of the received messages following `base 256` (to say). For sequence number `122`, we return IPv4 `0.0.0.122`, and for sequence number `257`, we return IPv4 `0.0.2.0`.

This should be enough planning for now, I guess.

I haven't used Go before in any project and want to learn it.
What better opportunity than this right here? Let's Go!

