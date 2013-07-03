---
layout: post
title: DOM Tests Landed -- Finally!
tags:
- mozilla
- seneca college
- open-source
- cdot
---

Finally, we have landed the [DOM tests](https://bugzilla.mozilla.org/833386)!
We've been working on them for a long time and it's taken a long time to sort
out all the issues we've had. We had a few major problems after we got the tests
ready to start being reviewed.

The first was that for tests that deal with cues we have to wait for them to be
loaded before starting the test. What we thought this entailed initially was
listenining for the MediaElement's "loadedmetadata" event as
[in the spec](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#event-media-loadedmetadata)
this is where it says the cues *should* be loaded. We quickly discovered this
isn't the case as we haven't implemented the bits that deal with that for WebVTT.
What we ended up doing was to
[implement the HTMLTrackElement's ReadyState](https://bugzilla.mozilla.org/show_bug.cgi?id=882535).
The ReadyState basically tells if the track is loading, loaded, or errored.
After implementing that it was easy enough to hook the cue test into it by
re-queueing the test until the state is set to loaded.

The other major problem was that we were failing on debug builds as we were
getting an assertion that the HTMLTrackElement wasn't a DOMWindow or DOMNode...
which is basically impossible. The problem was that we were doing
HTMLTrackElement's Query Interface implementation wrong. We got that
[sorted out fairly quickly](https://bugzilla.mozilla.org/show_bug.cgi?id=882817).

The last and most troublesome [bug](https://bugzilla.mozilla.org/show_bug.cgi?id=884884)
we had was that cue tests were failing on Android builds. This was weird because
when you run Nightly on Android you see subtitles. It took a while to fix this
too because Firefox's build system was broken on the newest Android SDK and Java
Development Kit. There was a workaround, but I never got it to work. What we
ended up doing in the meantime was to split out the tests that don't deal with
cues and land those. So we made some progress. Eventually on a hunch I did a
try-push to test whether or not "loadedmetadata" was being called (we were still
using this on top of the  HTMLTrackElement::ReadyState stuff) and, voila, there
was the problem. Talking to [Chris Pearce](http://blog.pearce.org.nz/search/label/mozilla),
it turns out that on Android they set preload=none to limit data usage. The
solution was to either remove the "loadedmetadata" stuff and just use the
ReadyState bits or add preload=auto on the Media Element. I just ended up
removing  the "loadedmetadata" stuff.

I did eventually get an Android debug build going on my Nexus 7, but I'm still
unable to run Mochitests on it...

Until next time.
