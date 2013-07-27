---
layout: post
title: Changes -- A New Day; A New Parser
tags:
- mozilla
- seneca college
- open-source
- cdot
---

I haven't blogged for a while. Mainly because I've been crazy busy with what's
been going on with WebVTT. Long story short some unfortunate things happened
and we needed to start over again with a new parser and new code in Gecko to
make use of that parser.

Due to this I've been working some crazy hours and pushing hard on getting a new
JS WebVT parser implemented. With the combined efforts of [Andreas Gal](http://andreasgal.com/),
[Dave Humphrey](http://vocamus.net/dave/), [Ali Dallal](http://alicoding.com/),
and myself we managed to get it done within a week and a half.
[Check it out](https://github.com/andreasgal/vtt.js) where it lives on GitHub.

I focused mainly on creating the test suite for the parser and fixing any bugs
that were found. While the parser is pure JS and designed to work in the browser
we created our test suite using Node (which can't work in Gecko as it runs on 
V8). This is the first time I've really worked with JS extensively and I have
to say from working with it that it's honestly a joy. Especially Node.js.

This last Sunday the bulk of the tests and the parser were finished and so I've
been working on getting a working patch to integrate the parser into Gecko this
last week. I got that [done today](https://bugzilla.mozilla.org/show_bug.cgi?id=895091).
Just in the nick of time too as I'll be on vacation all next week (in the
Carribean, yay!). All that's left is to go through review and land this sucker.

A lot has changed with our ideas about the parser. This time around the parser
is meant to focus on being a spec compliant parser. No error reporting, etc.
It's also going to be much more browser centric. WebVTT was designed to work
with the web and so we should support that use case well. Some of the highlights
of the new parser are:

* Baked the DOM tree creation of cue text markup directly into the parser.
* Support for the new Regions section of the WebVTT spec.
* Easy test creation through JSON reference files for WebVTT markup. In other
words WebVTT cues can be translated to and represented in JSON by this parser.
* Testing streaming data via simulating chunks being passed into the parser in
the test suite.
* Decodes the data it receives directly through a TextDecoder() object that it
is passed, or a default one provided by Google. The TextDecoder API it supports
is the one laid out by the [#whatwg spec](http://encoding.spec.whatwg.org/#api).
* Simplified API and code. The parser is less than 600 lines of code.

One future goal that I think would be cool is to make as much of the parser deal
with the WebVTT spec as possible. For example, it could return TextTrackCues
directly, or could even apply CSS positioning, etc, to the DOM tree. We could
make this optional so that the consumer of the library could either get parsing
at the WebVTT level or at the browser level. Those lines are starting to blurr
more and more as the spec evolves, but I think it would be something we could
do.

The great thing about this is that even though we are making it more browser
centric it can still work with non-browsers due to JS being awesome.

Until next time.


