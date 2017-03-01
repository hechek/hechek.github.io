+++
date = "2017-02-16T22:12:15+09:00"
title = "Gynvael Coldwind - Garage4Hackers - Notes from March 2014"
tags = ["error"]
categories = ["hugo", "dev"]
description = "description test"
+++

Back in March 2014, Garage4Hackers had a live stream with Gynvael Coldwind. His talk was named "Data, data, data! I can't make bricks without clay" or a few practical notes on reverse-engineering. You can see the recording on youtube.

Here are my notes that I discovered from 2014.

Notes Garage4Hackers RE webinar by Gynvael Coldwind (Google)
His website: http://gynvael.coldwind.pl

If ASM is hard, translate it to C for yourself.

Read through the manual and try to write your own pseudo-C code to understand it better.

Trace things

So if there are a lot of jumps with instructions in the middle.
He wrote a GDB script to just go through the jumps and collect all of the instructions in between to simplify the assembly.
Some debuggers have tracing as a separate option. E.g. OllyDBG

http://pelock.com/products/obfuscator

Twitch died and missed this tip name.

If you are working on an strange platform (e.g. IBM S/390), look at the manual and find the op-codes and go through them and translate them for yourself.
Write a simple script to add instruction descriptions to the disassembled code. So you can have a description of what it does with every instruction and you do not have to go through the manual for each op-code again.

Be prepared to make your own tools

Diassembly Engines
His favorite is “distorm” hosted on https://code.google.com/p/distorm/. You can use it inside Python scripts to disassembler binaries from starting to end addresses.

Debug APIs
Talked about useful debug APIs. E.g. CreateRemoteThread.

Twitch went down again.

CPU Specific Stuff (x86)
Software breakpoints (CC aka int3).
You can run code generator for Linux run on Windows with some changes.
Change the dependencies, addressing and I/O.
Goes through an example of a hash function in a Linux binary and then writing C code to allocate memory address at a specific address and then copying the hash function from Linux and putting it on the memory and then calling the function from the C code in windows.
GDB is your friend even if you like others.
It runs on any modern OS.
Works with various GDB stubs (e.g. QEMU).
Is scriptable in Python and GDB script.
Is not well suited for anti-RE tricks.
Scripting, use Python. WinDbg - Olly - IDA - Immunity and GDB support.
