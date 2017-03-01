+++
date = "2017-02-16T22:12:43+09:00"
title = "Thick Client Proxying - Part 5: FileHippo App Manager or the Bloated Hippo"
tags = ["proxy"]
categories = ["app", "https"]
description = ""
+++

I have talked a lot about this and that but have done nothing in action. Now I will talk about proxying actual applications. I will start with something easy, the FileHippo App Manager. This app was chosen because it can be proxied with Burp, it does not use TLS and it has its own proxy settings (also works with Internet Explorer proxy settings). The requests are pretty simple to understand. I like the FileHippo website because it archives old versions of software. For example I loved the non-bloated Yahoo! Messenger 8.0 when I used it (it’s pretty popular in some places) and used FileHippo to download the old versions.

FileHippo App Manager turned out to be more interesting than I thought and this post turned into some .NET reverse engineering using dnSpy. Here’s what I talk about in this post:

The app contains the AWS SDK and a fortunately invalid set of AWS Access/Secret keys. Both the SDK and the keys are in dead code.
Requests have an AccessToken header which is generated client-side. We will discuss how it is generated.
The application has a “hidden” DEBUG mode which unfortunately does nothing special. We will discover how to enable it.
Note: I attempted to contact both Well Known Media (parent company of FileHippo) and FileHippo via their security addresses. security@filehippo.com and security@wkmedia.com do not exist. I contacted them via their only email on the Well Known Media website which is adsales@wkemdia.com and got no response. I tried to check the validity of the keys using the most non-intrusive way possible as discussed below and fortunately they were not valid so I went ahead and shared the adventure.

1. Ingredients
Windows 7 VM
FileHippo App Manager 2.0 beta 4
Burp free
JSON Decoder plugin from Burp’s App Store
dnSpy
2. Proxying
2.1 Proxy settings

Install and run the application. Click on the Settings gear icon to the left and then select the Connection tab to see the proxy settings.

Application's proxy settings
Application's proxy settings
As you can see, the application supports its own proxy settings and also can use IE proxy settings via the Auto-detect proxy settings for this network. It does not really matter which method is chosen, we can use both of these to point the application to Burp. Point it to Burp’s proxy listener (default is 127.0.0.1:8080), run Burp and then press Test.
