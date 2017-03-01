+++
date = "2017-02-16T22:12:20+09:00"
title = "Use Paimei Stalker or Similar tools (by Pedram Amini)"
tags = ["blog"]
categories = ["install"]
description = ""
+++

SetBP(Address, function_to_call_when_the_address_is_reached)
Twitch died again :(

Monitor the environment.
Use these tools. * Linux: strace and ltrace. * On Windows : Process Monitor.

Example from a CTF.
Mixer: It needed a certain library. So you have to install all dependencies. Use “ldd” to check if you have everything.

LD_LIBRARY_PATH environmental variable.
readelf -l binaryname
will show us which loader is needed for this library.

It loaded the application in GDB but could not debug.
Two solutions: 1. Attach after running, bad idea. 2. Manually enter CC CC at EP by ediuting the hex. 3. Then run it in GDB.

You do not need to analyze everything - probably the most important thing when doing RE
If you have a blackbox with a small-ish input/output space, let it run and look at output to find out what it does.

The tool might be wrong. Check the output.
Entropy is a good recon tool.
Know Crypto and other stuff and be able to recognize it.
I asked him for an alternative to IDA pro for x64 binaries. He didn’t have any alternatives as he uses IDA Pro.
