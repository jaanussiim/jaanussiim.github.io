---
layout: rock
title: J2MEDB version 0.2.0 has been released
tags:
- j2me
- j2medb
---

Updated version of J2MEDB has been launched on [j2mehandsets.com][1]. Main work has gone into 
usability and handset detection improvement.

On MIDlet side key mappings test was altered a bit. I hope that new graphics will make the test more 
understandable. Also it has been made clearer, that on every upload only data for current test will 
be uploaded. I was thinking about uploading all test data in one go, but because of media test crash 
on Nokia 7650 I decided to drop it.

On server side handsets approving and some minor data editing options have been added. Handsets 
listing by supported APIs has also been enabled. I tried to improve handset detection based on current 
data available, but probably more issues can come up in that area.

At the moment the database only contains Nokia and SonyEricsson phones, which is bad :) But I am 
working on plan for adding more handsets (also from different manufacturers) to database.

For next version I would like to add some simple wiki implementation for known issues and general 
test descriptions.

So, please, keep testing :P

[1]: http://www.j2mehandsets.com