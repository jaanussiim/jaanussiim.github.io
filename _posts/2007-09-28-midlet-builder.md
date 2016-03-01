---
layout: rock
title: Midlet-builder
tags:
- ruby
- j2me
---

**The idea**

First project by me will be [midlet-builder][1]. It is loosely based on idea of [J2MEPolish][2].

For some unknown reason J2MEPolish stopped working for me. Previously working builds were no longer 
generated. Also I was a bit unsatisfied by their database, but that issue will be addressed by 
another project. So, after a week of banging my head on the table, I gave up. I dropped it and 
started to work on my own version for a builder.

Initial version of midlet-builder was created in Java and during my 'day time' work. I started a 
personal version in Ruby after reading some articles about learning a new programming language 
every year. At first I was thinking about learning Lisp, but decided to go with Ruby. Maybe also a 
bit because of Ruby on Rails.

At the moment the codes is really terrible and I have not used Ruby to it's full potential. I have 
a feeling, that I have done everything the same way, as I would have done in Java. Only in 
different language.

**Functions**

At the moment midlet-builder handles:

* source files preprocessing
* localization handling
* resource files copying
* source files compiling
* preverification
* obfuscation
* packaging of MIDlets

What it's lacks is flexibility. It does everything the way I like it and how I have pictured it. I 
am hoping, with more input, to create a more flexible product.

**Future ideas**

At the moment the ideas I am playing with:

* applet generation (for demo purposes)
* more device specific preprocessing options
* configuration of different obfuscators
* resource files processing based on target device
* different packaging options (test for best option)
* device information from configurable locations (text files, database, etc)

I have a feeling, that this is only a beginning. Hoping to hear ideas from You also.

[1]: http://www.jaanussiim.com/redmine/projects/show/1
[2]: http://www.j2mepolish.org/