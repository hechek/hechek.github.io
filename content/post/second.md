+++
date = "2017-02-16T22:09:56+09:00"
title = "Why do I Need to Know How Proxies Work?"
tags = ["website"]
categories = ["css"]
description = ""
+++

Note that the RFC instructs clients to send the absolute-URI no matter what (even if they are using a CONNEC request) as we will see shortly.

The proxy uses this absolute-URI to forward the request to the endpoint (in this case Yahoo!). Both forwarding and TLS terminating proxies work similarly in this case because they both can look inside HTTP payloads.

HTTP proxy in action
HTTP proxy in action
Browser establishes a TCP connection to proxy.
Browser sends the HTTP request (with an absolute-URI) to proxy.
Proxy establishes a TCP connection to yahoo.com (using the absolute-URI).
Proxy forwards the HTTP request.
Proxy receives the response.
Proxy closes the connection to yahoo.com.
Proxy forwards the response to browser.
Proxy signals to close the connection (using FIN).
Connection between browser and Proxy is closed.
3.1.1. Why not Use the Host Header?
If you have done at least a bit of HTTP security testing (or have seen some HTTP requests), you are probably asking “why not just use the Host header?” That is a very good question and it was mine too. We are the proxy and we see the Host header, why do we need to use the absolute-URI instead?

The answer is backward compatibility with HTTP/1.0 proxies. This is hinted in section 5.4. Host of RFC7230:

A client MUST send a Host header field in an HTTP/1.1 request even if the request-target is in the absolute-form, since this allows the Host information to be forwarded through ancient HTTP/1.0 proxies that might not have implemented Host.

Later it instructs proxies to rely on the absolute-URI and ignore the Host header. If the Host header is different from the URI, then the proxy must generate the correct header and send it with the request.

3.2. Forwarding Proxy and HTTPs

But what about HTTP(s) forwarding proxies? How do they work?

Again let’s put ourselves in place of the forwarding proxy. We do not do TLS handshakes and just forward things around. After the user types https://www.google.com in their browser, it creates a TCP connection to us and then starts the TLS handshake. The first step of a TLS handshake is ClientHello discussed in RFC5246 section 7.4.1.2. (RFC5246 is essentially TLS 1.2).

ClientHello sent from browser to proxy
ClientHello sent from browser to proxy
Now I did not read the TLS 1.2 RFC completely and I doubt you need to either. As the proxy, we will see a ClientHello like this:

ClientHello as seen by proxy
ClientHello as seen by proxy
But we are a proxy and we should know what it means. Tools should be able to do this for us. In this case I used Netmon and it decodes the ClientHello like this:
