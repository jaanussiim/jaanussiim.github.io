---
layout: post
title: Blast from the past
tags:
- j2me
published: true
---

**Starting J2ME and games**

At the end of 2004 Aqris Software AS was looking into different technologies. Something that peaked
our interest was J2ME. As I had previously shown interest in game development I was introduced to it.
And this is how one man J2ME development team was born :)

**Balloon pop**

As first try we decided to make a Bejeweld clone. I came up with the balloons theme for it. Instead
of jewels we would use balloons with new ones floating up from bottom. And this is when fun started.

Coming to J2ME was a challenge. Wrapping my head around platform fragmentation... Finding ways how 
to work around them... But this was not my only problem. When I was finished then the game consisted 
of one big run method with gazillion if statements. It was a absolute mess. Who ever wrote that 
should have been shot in place.

After working through all these issues the game actually got published at Zed Italy. Aqris had
previous contacts with them and this is probably only reason why this deal happened. As I heard
nothing from business side, I assume it was a total flop.

**Duck-Up!**

Aqris had also other contacts. One of these was Sulake. Creator of [Habbo hotels][1]. They wanted
to have their version of Balloon Pop. For that all the application code went through a massive rewrite.
And the result was also internally something beautiful.

There was a unwritten rule about not using object oriented code in J2ME. Because of classes overhead,
size on disk and memory, and limited application JAR sizes this was frowned upon. But after I had seen
beautiful code I have never looked back. Always make maintainable code and figure out JAR size limitations
later.

Anyway - Duck-Up! was released in Habbo shops. At some point I did hear a number like 1000 downloads
in one month, but I never got it confirmed. 

Application promo from their site:

![Duck up!](/images/Duck-128.gif)

**Puzzle pipes**

I guess Duck-Up! had to be some success, because Habbo wanted another game. This time they came
with the idea. They wanted to have 'connect pipes' type of game. Backstory was - new hotel was almost
finished, but some plumbing work was still left. And your mission was to finish it before time runs out.

Basic app architecture from previous project could be used. But there was one challenging part, that
I never believed we can do. Animating the fluids running in pipes. On slow J2ME devices we could never
have kept the frame rate at acceptable levels. What I ended up doing was craziest fun code ever.
Internal pixels of pipe were counted together and as fluids did flow only pixels changing were updated.
Somehow this worked perfectly.

Again, I got no updates about app sales...

![Puzzle Pipes](/images/Puzzle_Pipes_128x128_v3.gif)

**This was when it clicked...**

Thinking back to that time... Figuring out how to shave few bytes off jar size so that it would fit 
into 64kB device limit. Finding common base for fragmented devices. Hunting down test devices. This
kind of tinkering was real fun. And that is where I got the mobile bug. Although there were a few years
between when it was only a hobby. I found my way back to it and am still going strong!

[1]: http://www.habbo.com/