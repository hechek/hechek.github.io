+++
date = "2017-02-16T22:01:19+09:00"
title = "Thick Client Proxying - Part 6: How HTTP(s) Proxies Work"
tags = ["test"]
categories = ["blog"]
description = ""
+++

In order to create our own custom proxies, first we need to know how proxies work. When I wanted to write a custom proxy tool (it’s a simple Python script) in Hipchat part3, I had to go back and learn how they work. I did not find such a resource online that looked at proxies from an infosec perspective. Most talked about how to configure caching or forwarding proxies and not much about MitM ones. I have briefly talked about it in the section 2 of the same post named How does a Proxy Work?. In this post I am going to take a deep(er) dive. I actually read some RFCs and they were surprisingly well written.

If you want to skip the intro, go to section 3.

0. Why do I Need to Know How Proxies Work?
That is a fair question. Most of the time, we pipe the browser to Burp and it works out of the box. However, if something small changes we will go into panic mode. What if the webapp uses a Java or Silverlight component and it has some quirky things? Another reason is for proxying thick clients because Burp as you have seen here is not just for webapps. My opinion is “if the application uses HTTP, you can Burp it.” Thick clients may not work out of the box when proxied (often just redirecting their traffic to the proxy is a pain). If we do not know how proxies work internally, we cannot troubleshoot the issues.

You are now convinced that you need to read this right? riiiiiiiiiiiiight?

1. Become One with the Proxy
It really helps to put ourselves in place of the proxy when reading this. At least that is what worked for me. Proxy does not know whatever happens in the system like an observer. As an observer we can just make decisions about what the proxy should do. Things like “the user typed google.com in their browser so the proxy must send the request to google.com.” Well, how does the proxy know that? The proxy cannot magically see the browser’s address bar.

1.1. What does it Mean?

We are the proxy. The only things we see are the requests/packets that the client (e.g. browser) and the endpoint send to us. We do not know anything else. And as a proxy we must decide what to do with the requests that we receive with only our knowledge.

Now that we are hopefully in the zone, let’s start.

2. Brief Intro to Two Types of Proxies
I am going to talk about two types of proxies here.

Forwarding proxies
TLS terminating proxies
The descriptions are not entirely accurate or detailed but are enough for our purpose. Of course this is not an exhaustive list. There are other proxies out there but these are the ones that we are interested in. To be honest we are only interested in TLS terminating proxies.

2.1. Forwarding Proxies

We have all seen them before. These are corporate proxies that we see and use everyday. If you are in a corporate environment, check the proxy auto-config (pac) scripts. Essentially it’s a text file that tells the application where to send the traffic and re-routes the traffic based on the endpoint. Usually if the endpoint is internal, things get routed normally through the internal network otherwise (requests sent over the internet) requests are sent to a forwarding proxy. You can see some examples at Microsoft Technet. From the point of view of the application, the forwarding proxy is sitting between the internal network and the internet.

Based on the name, these proxies just forward packets and cannot look inside encrypted payloads (e.g. TLS). From the point of view of a typical forwarding proxy, an established TLS connection is just a bunch of packets with random looking TCP payloads.

2.2. TLS Terminating Proxies

Burp is the prime example of this type. If you know what Burp does (and you probably do because you are reading this), you know what a TLS terminating proxy does. These are proxies that usually MitM the connections and unwrap TLS to look inside the payloads.

They could be applications like Burp or Fiddler which are usually used for (security) testing. Or could be appliances like Bluecoat or the SSL decryption module of Palo Alto Networks’ “thing” (whatever it is named). Usually these appliances are used for deep packet inspection.

You could make Burp work like a forwarding proxy by adding all endpoints to Burp’s SSL Pass Through. This is useful for troubleshooting connections.

2.2.1. It’s not Always TLS
True. Sometimes our proxy decrypts (or decodes) layers of non-TLS encryption (or encoding). I am classifying all of these proxies under this category because TLS has become the most common way of protecting data in transit.

3. How HTTP(s) Proxies work
Now we get to the main part. In all examples we have a browser that uses a proxy (via some proxy settings) and the browser knows that it is connected to a proxy (I will talk about this later).

3.1. HTTP Proxy

In this case the browser is using plain HTTP (meaning there’s no TLS). Both forwarding and TLS terminating proxies work similarly in this case.

Let’s assume we have typed http://www.yahoo.com in the browser. Let’s forget that we get a 302 redirect in the real world and assume yahoo.com is available over HTTP. I probably should have used example.com instead but I am lazy and don’t want to create the diagrams again.

The browser establishes a TCP connection to the proxy (the famous SYN-SYNACK-ACK) and then sends the GET request.
