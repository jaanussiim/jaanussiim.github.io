---
layout: rock
title: Continue delivering using Xcode8 beta
tags:
- swift, xcode, beta, wwdc
published: true
---

**Make the switch!**

WWDC16 is not over yet and we have seen a lot of exiting things delivered. (I'm most exited about CloudKit record sharing). With most of the new technologies you can't really show them off before iOS10 has been released in the fall.

On tools side there is a lot in the new Xcode8 beta, that you should take advantage ASAP. And I'm talking just about better Swift support. This alone should be worth the switch. Of course you can't use beta version of Xcode to build distribution packages, but for everyday coding it's perfect.

**Two Xcodes**

The plan is to use Xcode8 for every day development and to use Xcode7 when ever you need to do new release of existing app. The only catch is, that Xcode8 does not support Swift2.2 that we need to make active releases on Xcode7. Luckily the differences between Swift2.2 and Swift2.3 are not that big.

**Convert the project**

When upgrade to Xcode7 forced you to also adopt Swift2, upgrading to Xcode8 will not be so dramatic. When opening Swift project in Xcode8, you will be given an option to migrate to Swift2.3 or Swift3.

Migration to Swift3 is something big that you really can't do at the moment if you plan to continue delivering apps before iOS10 has been released.

Migration to Swift2.3 will is significantly smaller step that allows easy support for Swift2.2

Swift2.3 has minor API changes when interacting with Foundation classes. More methods will return optional types. One example is NSURL. URLByAppendingPathComponent will now return optional result. There is a simple strategy to handle these differences - using preprocessing:

{% highlight swift %}
#if swift(>=2.3)
    let fileURL = dir.URLByAppendingPathComponent("log.txt")!
#else
    let fileURL = dir.URLByAppendingPathComponent("log.txt")
#endif
{% endhighlight %}

As this is a temporary solution until Xcode8 GM drops, making code a bit ugly for that time to use latest and greatest tools, is well worth it.

I took a bigger Swift only project for reference, to see how many issues I could expect between Swift2.2 and Swift2.3. This project has 550 files with around 43k lines of code. When compiling it I found 15 issues where some preprocessing was needed.

My advice would be not use the migrator for Swift2.3. Change project settings for *Use Legacy Swift Language Version* to YES. (Same for cocoapods, if needed). And see what fails. Migrator would take you to Swift2.3, but we need to add manually preprocessing to support Swift2.2 and Swift2.3 in same code base.

**Conclusion**

For me just the benefit of better Swift support for next months until Xcode8 GM drops is worth the effort. Swift was also the biggest hurdle to try the new version of Xcode. After this has been conquered, I can also try other new tools without compromising my need to continue work on supporting existing projects.