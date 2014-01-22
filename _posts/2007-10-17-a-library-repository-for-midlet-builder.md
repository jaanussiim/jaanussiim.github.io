---
layout: post
title: A library repository for midlet-builder
tags:
- other
---

One feature for [midlet-builder][1] would be dynamic jars selection for source compilation. The 
selection would be handled based on supported device properties. This should, in theory, add 
additional security level, that the compiled code is also suitable for the device. You could 
immediately see, if you are using classes from unsupported packages.

First of all I need a cheap location for the repository. And at the moment [Amazon's S3][2] looks 
like a perfect solution. It is lacking possibility for browsing, but I'm sure there are ways around 
it.

After playing a bit with S3 I have one tip for you - go straight to [s3sync][3]. In my opinion the 
fastest way to test S3. I started experimenting with Ruby code. And it was really not going 
anywhere... But it was my problem - some times I just can't stand, if something is holding me back 
from working on my ideas.

Anyhow - the tough part will be dependency handling. At the moment I'm leaning towards using [Ivy][4]
for it. It could be used as standalone from command line. What I do not like about it is, that it 
will create additional dependency inside midlet-builder. Already I'm relying on too many external 
programs, that can't be configured.

As another solution I should check dependency handling in [Raven][5]. If I only found some time for it...

And of course midlet-bulder could also handle the dependency resolving. As I figure - it does not 
have to me something fancy... Just some dirty version comparing and http requests... But at the 
moment it should be really low priority... I have more important issues to resolve.

[1]: http://www.jaanussiim.com/redmine/projects/show/1
[2]: http://www.amazon.com/S3-AWS-home-page-Money/b/ref=sc_fe_l_2/103-8634166-2789454?ie=UTF8&node=16427261&no=342430011&me=A36L942TSJ2AJA
[3]: http://s3sync.net/wiki
[4]: http://ant.apache.org/ivy/
[5]: http://raven.rubyforge.org/