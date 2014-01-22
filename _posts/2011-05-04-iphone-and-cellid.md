---
layout: post
title: iPhone and CellID
tags:
- ios
- cellid
---

Since my first contact with [GIS][1] in general and after finding [OpenCellID][2] database, I have 
developed an obsession with [CellID][3]-s. I need to map them. I need to see mapped cell towers on 
the map. I need to figure out the range of cell towers. And if the tower was 2G only or was also 
supporting 3G. And of course I needed to create my own GIS server to support all this.

In the old days You could access mobile towers cell id on almost any feature phone. With J2ME on 
SonyEricsson and  Nokia phones it was a cake walk. With Android everything got even better. Loved 
my HTC Hero (European version with the chin). But since my day job consists mainly of iOS 
development, I have also moved to iPhone 4. And the problem is - there is no way to access CellID 
data programmatically on iOS. You can access MNC and MCC, but no CellID.

As a developer this little detail can't stop me from writing a map application with CellID mapping 
support. I still have the old Hero laying around and with the iOS 4.3 update I got the most 
brilliant idea - would it not be possible to connect Hero to iPhone's WiFi hotspot and access 
needed data somehow this way?

Initial tests have  been positive. Using [i-jetty][4] on Android I was able to access the device 
from iPhone providing WiFi connection. Now I just need to check if I can also access required 
device data from Jetty servlets. If everything works out, I could connect multiple Android devices 
simultaneously and map all towers for operators in Estonia at the same time.

What will I do with the data? Probably just some pretty graphics  and nothing more. 
It's more about the journey...

[1]: http://en.wikipedia.org/wiki/GIS
[2]: http://opencellid.org/
[3]: http://en.wikipedia.org/wiki/Cell_ID
[4]: http://code.google.com/p/i-jetty/