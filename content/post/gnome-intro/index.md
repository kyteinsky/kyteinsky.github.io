+++
author = "Anupam Kumar"
title = "GNOME Introductory Post"
date = "2022-06-12"
description = "I have been selected as a GSoC Intern at GNOME Foundation"
tags = [
	"gsoc",
	"gnome",
]
categories = [
	"gsoc",
]
aliases = []
image = "GNOME-logo-500x316.png"
+++

I will be working as a Google Summer of Code Intern at GNOME Foundation, and my project will be to add Chromecast support to the already very cool [GNOME Network Displays app](https://gitlab.gnome.org/GNOME/gnome-network-displays) that has Miracast support as of now. It can be installed through [flatpak](https://flathub.org/apps/details/org.gnome.NetworkDisplays) as well.

### Details

Linux desktop users will be able to cast their screens to Miracast and Chromecast devices. For the Android TVs that feature both of them, it would be wise to opt for the Chromecast path when both the devices share the same router (i.e. are on the same network) since Miracast would require setting up a WiFi-Direct connection to function (may be faster with less latency, may not be worth the trouble).

I believe this will see the light of day with very helpful and experienced mentors, **Claudio Wunder** and **Benjamin Berg**.

### Progress

We started by looking into the Chromecast documentation (an adventure in itself). So there are two ends to deal with - the sender one and the receiver one.

#### The receiver end

There are two options here: **Styled Media Receiver** and **Custom Web Receiver**.

The **Styled Media Receiver** does everything for us. It hosts the receiver application (an HTML5 app) and provides all the default styles and functionality. If we want to change the styles and/or the logo, we are free to host them on an HTTPS server, and we'll be done.

With everything it offers, it has limited still quite adequate media support, including playing video files, audio files, streaming video, and audio, displaying images and opening specific apps on the TV (like YouTube).

Our main job is to "stream" our desktop to the Chromecast device. For this purpose, the **Styled Media Receiver** supports three streaming protocols:
- Dynamic Adaptive Streaming over HTTP (DASH)
- Apple's HTTP Live Streaming (HLS)
- Microsoft's Smooth streaming

We drop considerations for the Smooth Streaming protocol here because of the lack of feature differences compared to the other widely supported protocols, except that HLS and Smooth Streaming support pre-loading by default.

So we end up here with two choices: DASH and HLS. Now, HLS is widely supported; on the other hand, DASH is codec agnostic for both video and audio. In terms of latency, both are said to have similar latencies that depend on the segment duration we decide upon.

As per the HLS's RFC (linked below), the `EXT-X-TARGETDURATION` tag in the playlist file (or manifest file) accepts a decimal integer value. It dictates how much the maximum duration of each segment can be (rounded off to the nearest integer). It is suspected to be similar to DASH, although its RFC does not clearly mention so. We better find out by actually experimenting and streaming.

Reducing the segment size too much can cause the bitrate to bump up and the stream quality to degrade thanks to all the added keyframes for each segment, so we need to test what suits this live stream the best.

---

Next up, we take the discussion to the **Custom Web Receiver** and the conclusions from some of our tests with the Chromecast streamings using the Command and Control (CaC) Tool, which is a sender app provided by Google for testing and debugging Web Receiver apps.

We want to test out and know more about other protocols not supported out of the box in Chromecast but can work fine with a Custom Web Receiver: SRT, RTSP and WebRTC.

Thanks to Benjamin and Claudio for all the help!

---

Relevant Links:
- [Chromecast Docs](https://developers.google.com/cast/docs/)
- [Styled Media Receiver](https://developers.google.com/cast/docs/styled_receiver)
- [Streaming Protocols Supported by Web Receiver](https://developers.google.com/cast/docs/web_receiver/streaming_protocols)
- [DASH vs HLS by Toolbox](https://www.toolbox.com/tech/programming/guest-article/mpeg-dash-vs-hls-which-one-should-you-use/)
- [Common Video Streaming Protocols](https://www.dacast.com/blog/video-streaming-protocol/)
- [Blog on Segment Length in DASH and HLS](https://bitmovin.com/mpeg-dash-hls-segment-length/)
- [Blog on Segment Duration in DASH and HLS](https://streaminglearningcenter.com/learning/choosing-the-optimal-segment-duration.html)
- [HLS RFC: EXT-X-TARGETDURATION tag](https://datatracker.ietf.org/doc/html/rfc8216#section-4.3.3.1)
- [DASH RFC](https://datatracker.ietf.org/doc/html/rfc7933)
- [CaC Tool](https://developers.google.com/cast/docs/debugging/cac_tool)

&nbsp;


