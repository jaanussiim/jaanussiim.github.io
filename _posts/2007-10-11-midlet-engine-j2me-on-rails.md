---
layout: rock
title: Midlet-engine - J2ME on Rails?
tags:
- j2me
---

Ok, ok - the title is a bit bold. But the idea behind the framework for J2ME games should be the 
same - to handle all common behavior and let You just create.

[midlet-engine][1] is based on the idea that only places, where games code will change, are in main 
menu animation and game logic/rendering. Everything else can be handled by common code and can be 
reused. There is no need for creating screens flow handling, localization, resources loading, etc. 
from scratch for every new game. Just create a common framework for implementation, that would be 
reused, and innovate. The additional benefit of a framework would be, that it would contain all the 
knowledge about device specific bugs and workarounds for these.

Currently midlet-engine handles:

* localization
* screens flow (from intro screen to outro screen)
* custom font implementation
* a custom sprite implementation

Should handle:

* network communication
* generic high scores handling
* generic saved game handling
* handling and separation for thread driven and event driven MIDlets
* resource packages for screen/level

The problem with the midlet-engine at the moment is, that it's not really flexible. To use it for 
event driven MIDlet I had to use some hacks in midlet-builder, to ignore not needed classes. 
This should be some how configured and handled better.

Under resource packages I was thinking that there should be a way to determine automatically, 
what resources are needed by every step of the application. Basically between load screens. If it 
could be detected, then we could combine these resources in different resource packages, to gain 
some jar space. But at the same time the resource packages wold not get too big and make loading 
noticeably slower...

But the method for detecting the used resources is basis for another project...

So many ideas and so little time...

[1]: http://www.jaanussiim.com/redmine/projects/show/2