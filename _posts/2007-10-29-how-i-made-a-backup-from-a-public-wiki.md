---
layout: post
title: How I made a backup from public wiki
tags:
- other
- ruby
---

**Background**

Lately the [J2ME forums wiki][1] has been under constant spam attacks. As the owner of the forum 
seems to be rather busy, to implement some spam protection, there was an idea to create a backup 
from the data in wiki before spam. I have no access to the wiki server, so I had to use other means 
available. [Hpricot][2] to rescue.

**Know the enemy**

To me it looked like there is no way to be sure, if site contains spam or not. Some users 
experimented with adding spam links into hidden div in hopes that spam robot will be fooled. But it 
had no effects. So we may have spam links on all pages. There was also no 100% sure way to know, 
when spam posts started, so I opted for full backup of the wiki.

So I needed a history for all discovered pages. In [MediaWiki][3] the pages history is available using address:

{% highlight html %}index.php?title=#{page_name}&amp;action=history{% endhighlight %}
As I had no idea how many pages there were in history I had to add parameters
{% highlight html %}&amp;limit=1000&amp;offset=0{% endhighlight %}
to the url. As default I would have gotten
{% highlight html %}limit=50{% endhighlight %}
From history page I searched links for every available revision for the page. Armed with this 
knowledge I could scan every revision of the page, search for additional internal links, and save page content.

**The code**

Can be downloaded from [s3 bucket][4]

I created a Parser object, that will be initialized with the wiki url. After that You just call the
{% highlight ruby %}parse(entry_page){% endhighlight %}
method and it will start making backups of the wiki pages. Saving these into 'data' directory. All 
saved pages will have modification date in their name.

**The result**

After 10 hours of running and 3GB data transferred I had 850 MB of knowledge in 'data' directory. 
Probably a lot of it is also spam. The next step should be converting these pages automatically to 
[DocuWiki][5] format and removing spam. At the moment I will probably create clean pages by hand and look at it later.

I also tried to use urls blacklist from [blacklist.chongqed.org][6] but in my experiments it turned 
out to be rather useless. The latest urls were not listed there... In the end it actually did not 
make any difference. I just made a dump of data on the page.

Was it worth it - for me definitely. I added my url as user agent in request and until now nobody 
from J2ME forums has also complained.

**Lessons learned**

Hpricot made the task a lot easier. I must admit, that I had some small problems with it at first... But it got better.

In the parser, there is also one redundant step. There is no point for getting internal links from
actual history page. This data is also available on edit page, that will be visited anyways... If 
this step would be removed, then the parser could have completed the backup in 50% less time 
transferring 50% less data. Too bad I realized that next morning, when script was already running.

And the revelation today - I need to start commenting my code better. You never know, when it will get published.

Anyway - have fun! Now I'm going back to mobile development.

[1]: http://www.j2meforums.com/wiki/index.php/Main_Page
[2]: http://code.whytheluckystiff.net/hpricot/
[3]: http://www.mediawiki.org/wiki/MediaWiki
[4]: http://jaanussiim-blog.s3.amazonaws.com/wiki.zip
[5]: http://wiki.splitbrain.org/wiki:dokuwiki
[6]: http://blacklist.chongqed.org/