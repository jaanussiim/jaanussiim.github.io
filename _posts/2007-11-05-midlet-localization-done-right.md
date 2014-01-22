---
layout: post
title: MIDlet localization done right
tags:
- j2me
---

I have used two ways for MIDlet localization since I started coding J2ME. I will be describing my 
changes in localization handling based on demand from clients.

Actually I have used three solutions. The first solution was having all your strings hard coded in 
source files and never change them. It worked until we found a client in Italy and we needed to 
support two languages.

Then we opted on using the tools that we knew at the time. We used [ant's replace task][1] for 
processing translation strings. It worked really well at that time. Of course we had to create two 
builds for every targeted phones group, but we could handle it. What made things a bit difficult 
was that the build system we used then produced some times broken builds. So the release schedule 
also included me running every build in emulator. Just in case. If some code path did not work we 
made a new build for that device and everything was fixed.

Then we got a client, that wished to support twelve languages. It was clear, that our procedure at 
that time did not work anymore. I think that the idea for current implementation (used also in 
midlet-builder/midlet-engine) came from J2ME-Polish. It was how they were handling localization 
(disclaimer - I can't remember, if I have ever actually looked at localization code in J2ME-Polish), 
or at least how I understood it.

In first step of localization midlet-builder will generate two files from input directory. The 
actual localization file and a file used by preprocessor later on. The localization file generated 
has following format:

{% highlight java %}
< number of languages > at the moment this is byte
< number of translation keys > number if different strings used
    in MIDlet. This number does not include some special keys
    described later.
{% endhighlight %}

Next we will have stings used on localization screen for language selection by user

{% highlight java %}
< language codes > two character language codes that can be used for
    automatic language selection on the phone (comparing it to
    microedition.locale property. A note – this will not work on
    some Samsung phones).
< language names > localized languages names. Preferably in native language.
< special select keys > also used on localization key. If you
    were moving up/down in languages list, 'select' string will
    be shown in highlighted language.
< help strings >
< about strings >
< all keys for every language > This is where all translation
    strings for every language are entered. All the keys will be
    same for every localization set. And they will be entered in
    the same order.
{% endhighlight %}
    
The other file generated is used in source files preprocessing. We'll call it 'key_map.txt'. It has
key-number pairs for key index in localization set. For example 'menu.help = 0' would mean that 
translation for 'HELP' string in main menu is located in first position for every language.

In the source code we have languages screen for language selection. It will load language codes, 
language names and select strings for displaying these on the screen

{% highlight java %}
    InputStream is="".getClass().getResourceAsStream("/loc.bin");
    DataInputStream dis=new DataInputStream(is);
    final byte languages=dis.readByte();
    languageNames=new String[languages];
    languageCodes=new String[languages];
    selectStrings=new String[languages];

    //ignore translations length
    dis.readByte();
    for(int i=0;i &lt; languageCodes.length;i++){
      languageCodes[i]=dis.readUTF();
    }

    for(int i=0;i &lt; languageNames.length;i++){
      languageNames[i]=dis.readUTF();
    }

    for(int i=0;i &lt; selectStrings.length;i++){
      selectStrings[i]=dis.readUTF();
    }
{% endhighlight %}
    
Here the translation keys length is ignored, because it has no meaning here. Maybe it should be 
moved to different position.

Because all languages are shown in the order, as they are in localization file, we can search for 
used language keys start position, after user has selected one. Until now no actual translations 
have been loaded. To load the keys into array we skip all special keys and also all different 
language keys that can come before selected language.

For accessing localization keys from code we have two methods in translation handler: 
get(String keyName) and get(int keyIndex). The first method will have no implementation and 
is there only for compiler and will be replaced by translation preprocessor. The preprocessor will 
replace all get(“key.name”) calls with get(index) calls based on values loaded from 'key_map.txt'.

Because help and about text are usually not displayed that often and take more space in memory, 
they are handled a bit differently. They are loaded 'on demand' and are not kept in memory (unlike 
other strings for loaded language).

So this is my opinion of 'localization done right'. I should really create a wiki page describing 
the process and all included file formats.

In the future I would also like to add localization loading from server and maybe just keep 
actually used translations in memory (for example only main menu elements). But this would require 
an automated way for detecting keys used on every screen... Looks like an idea for future projects!

[1]: http://ant.apache.org/manual/CoreTasks/replace.html