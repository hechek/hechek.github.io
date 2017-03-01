+++
date = "2017-02-16T22:12:24+09:00"
title = "Windows Netsh Interface Portproxy"
tags = ["syntax"]
categories = ["blog", "deploy"]
description = ""
+++

I thought I had found the Windows iptables with Portproxy but I was wrong. But I learned something neat in the process and I am documenting it to access it when I need it.

Portproxy allows you to listen on a certain port on one of your network interfaces (or all interfaces) and redirect all traffic to that interface (on your computer) to another port/IP address.

The to that interface is the limitation that unfortunately kills it. This will be a short post.

A typical Portproxy command is like this:

netsh interface portproxy add v4tov4 listenport=9090 listenaddress=192.168.0.100
 connectaddress=192.168.1.200 connectport=9095
So I thought I could change the listenaddress:listenport and redirect all outgoing traffic to that IP:port to wherever I wanted (e.g. localhost) and not use the Windows etc\hosts file. But I was wrong, the command creates a listener on the interface with the listeneraddress IP and redirects all TCP traffic. In other words, the listeneraddress needs to be the IP of an interface of your machine. The traffic needs to be destined for your machine to be able to be redirected with this command. Well bummer.

Install IPv6 Support

Before we start, install IPv6 support in your OS. According to KB555744 Portproxy may not work if IPv6 support is not installed.

Why? I don’t know but I think because Portproxy supports both IPv4 and IPv6 addresses which is a good thing. If you look at the MSDN link you can see the four variations that mix v4 and v6 addresses.

Portproxy in Action

Let’s do something simple first, we want to redirect anything that goes to 127.0.0.1:8888 to Google.com:443.

Open an admin command prompt and run this command (protocol=tcp is optional because Portproxy only supports TCP):
