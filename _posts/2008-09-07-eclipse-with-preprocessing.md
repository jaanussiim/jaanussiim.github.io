---
layout: post
title: Eclipse with preprocessing
tags:
- j2me
- eclipse
---

When working with code that was written by somebody else, one of the most annoying issues for me was
inconsistent code formating. Actually in this particular case the indentation was totally screwed up.

Only option was automatic formatting with Eclipse. But there was a problem with preprocessing 
directives in code. They all got screwed up, a space was added between // and #, and nothing worked. 
To my luck (and for the luck of other J2ME developers :P) there is a setting 'Enable line comment 
formatting' buried inside code formating settings. With this option disabled, preprocessing worked 
as a charm.

This particular code was also using code preprocessing in imports, that got still screwed up. But 
this should be considered a bad style anyway. And it is really not needed. You should preprocess 
code/class usage and let obfuscation do rest of the work – removing unneeded imports/classes.