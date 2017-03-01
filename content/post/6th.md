+++
date = "2017-02-16T22:12:28+09:00"
title = "Portproxy in Action"
tags = ["gitlab"]
categories = ["gitlab-cl"]
description = ""
+++

Let’s do something simple first, we want to redirect anything that goes to 127.0.0.1:8888 to Google.com:443.

Open an admin command prompt and run this command (protocol=tcp is optional because Portproxy only supports TCP):

netsh interface portproxy add v4tov4 listenport=9090 listenaddress=127.0.0.1
 connectaddress=216.58.217.78 connectport=443 protocol=tcp
Remember to remove the new line (I have split the command into two lines for better readability).

This command creates a listener on localhost:9090 and forwards all traffic to 216.58.217.78:443 (which is Google for me - you will probably get a different IP address if you ping it).

We can display all current portproxy listeners using netsh interface portproxy show all.

In the same admin command prompt run this command to see the listener: netstat -anb | findstr 9090.

Portproxy to Google
Portproxy to Google
Now open a browser and navigate to https://localhost:9090.

Obviously bad certificate
Obviously bad certificate
Accept the security exception and we will see:

Somewhere in Google land
Somewhere in Google land
Not exactly Google.com but you know what we accomplished.

Other Uses

Apart from doing failed tricks we can do other things with this. As we saw we can redirect local resources to remote ones. Another is port changing, we can redirect the traffic to localhost via different means (e.g. hosts file) but the port is still the original one used by the application. Using this we can redirect the port to another one on a remote machine (e.g. a VM running a proxy tool). In other words we will not need the Traffic Redirector Burp extension anymore.
