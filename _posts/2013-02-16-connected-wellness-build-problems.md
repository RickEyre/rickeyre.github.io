---
layout: post
title: 'Connected Wellness: Build problems..'
tags:
- seneca college
- open-source
- cdot
status: publish
type: post
published: true
---
For the last week I've been trying to fix some Xcode build problems that we've been having. After I upgraded the project to 2.3 Carl tried to run it on the simulator and it didn't work. After doing a lot of digging I figured out that I only provided an armv7 static library of Cordova. What this meant was that we couldn't run it on the simulator because it runs on the Mac which uses an x86 processor. What I ended up having to do was add two versions of the library to the project, one compiled for x86, and one compiled for armv7, and then make the project conditionally link them in based on if we were building for x86 or armv7. This was kind of tricky because I didn't know at first how Xcode manages this stuff. I finally discovered that you can add a build condition underneath other linker flags in the target's properties that will allow you to do this.

After this was all done I had to take care of adding in the 2.3 version of the AppDelegate and ViewController because these weren't in the project when I switched over to 2.3. They had been taken out, by me I believe, I forget why. Once that was done I began to get some problems with the linker not being able to resolve some of the symbols in the Cordova library that had to do with extending classes. Fixing this required me to add "-force_load" to the linker flags. This specified to Xcode to load the entire library, whereas if you don't put that, its default mode is to not load extensions... strange.

There's still one last problem with the build that Carl has informed me of. The problem isn't currently surfacing on my own machine so we're going to have to do some more digging.

Final, thing that I did was attempt to simplify the Command hierarchy by making ReturnableCommand just a Returnable and then having BluetoothCommand implement the Command protocol. This simplifies it a little because ReturnableCommand no longer has to be an abstract class. It also makes sense because an object that is returnable to the Cordova library isn't necessarily a command. This way in the future it will be possibly to have many different types of objects be able to return a plugin result back to Cordova.
