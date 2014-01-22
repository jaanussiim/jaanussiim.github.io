---
layout: post
title: Automated mogenerator execution
tags:
- ios, core data, mogenerator
published: true
---

**'Run script' phase**

After always doing manual execution of mogenerator on command line there was a time for change.
Also when ARC was used and additional execution flag needed to be added. Somehow it became too
cumbersome.

Probably the easiest setup was adding 'Run script' step to project to pick up changes
in core data model. That was nothing special, just

{% highlight bash %}

mogenerator -m mTimer.xcdatamodeld/mTimer3.xcdatamodel -O Classes --template-var arc=true

{% endhighlight %}

But the advantages were huge. Just hitting CMD+B modified entity files to include latest
changes. This eliminated one manual step that took time and could also introduce some errors.

Also executing mogenerator as build script had the advantage, that anyone building the project
was on the same page.

**mogenerator not installed**

Taking the simple approach had an issue. Building project had now a new required dependency - 
mogenerator. Even if model was not touched, building failed when mogenerator executable was
not found. 

As a developer you could quickly find out why it fails, but when somebody else is involved...
I had an experience where remote client wanted to run code in simulator to do some demo videos. 
But the project would not build. Client could not communicate why that happened and I was 
also clueless because on my machine everything worked. Month later we met with client
face-to-face. When he showed what was happening, then it was a three minute fix.

But this situation should not have happened. All mogenerator generated files are anyway
included in source control. It should only be needed when developer updates core data model.
Just building the project should work out of the box.

That did lead to a modification of mogenerator execution script

{% highlight bash %}
type mogenerator >/dev/null 2>&1 || {
    echo >&2 "warning: mogenerator not found. You will need it when changing core data model";
    exit 0;
}

mogenerator -m mTimer.xcdatamodeld/mTimer3.xcdatamodel -O Classes --template-var arc=true

{% endhighlight %}

Before mogenerator is executed we try to figure out if it's available. If it's not available
show a warning and abort execution. And if you know how to modify core data model, then you
should also know how do resolve given warning :)

'warning:' part in the message is required to also show this as warning in Xcode build log.
When you are keeping project build warnings at zero, then a new one appearing should be 
quite easy to notice.

**Adding new version to data model**

There are still times when the script needs to be modified. When a new version is added to
model, then the script needs to be pointed to new location. Otherwise mogenerator will check
for updates in old version and developers could be puzzled, why changes in new version are
not appearing in source code.

Maybe switching to new model version could be done automagically? Your model folder contains
also file called '.xccurrentversion', that will be updated when you switch to new version in
Xcode.

Using PlistBuddy executable we could read the value from version file. After more modifications
script looks like this (also extracted some variables for clarity)

{% highlight bash %}
PATH_TO_MODELD="mTimer.xcdatamodeld"
OUTPUT_PATH="Classes"

type mogenerator >/dev/null 2>&1 || {
    echo >&2 "warning: mogenerator not found. You will need it when changing core data model";
    exit 0;
}

CURRENT_VERSION=$(/usr/libexec/PlistBuddy -c "Print :_XCCurrentVersionName" "${PATH_TO_MODELD}/.xccurrentversion")

MODEL_PATH="${PATH_TO_MODELD}/${CURRENT_VERSION}"
echo "Used model path ${MODEL_PATH}"

mogenerator -m $MODEL_PATH -O $OUTPUT_PATH --template-var arc=true
{% endhighlight %}

Now, when ever Xcode is pointed to new model version, mogenerator will also use that version.

**What more could be done during mogenerator execution**

When the project lives longer, then it may happen, that people may end up on different versions
of mogenerator. That could generate some noise when these generate slightly different output.
Script could check expected and actual mogenerator version.

Other area of improvement - checking orphaned files. When project goes on and model evolves,
sooner or later some entities will be removed or renamed. In that case script could check
for files that no longer have responding entities.

**Conclusion**

Adding mogeneration as script execution to project has been one of these small things that
has taken some mundane manual tasks from developer. With a few tweaks you can just add the
script at projects start and never think about it again.

Full script is available for modifications/improvements as [Gist][1] on GitHub.

**Update**

With version 1.27 mogenerator picks up model version automagically. But there is a small issue.
If version file is present then everything works as expected. Without it mogenerator fails.
You need to point it manually to only existing model file. This has been fixed in 
[mogenerator GitHub repository][2] and should soon be available also in packaged release.

[1]: https://gist.github.com/3597754
[2]: https://github.com/rentzsch/mogenerator