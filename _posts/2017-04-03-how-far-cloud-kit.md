---
layout: rock
title: How far could you go using CloudKit?
tags:
- ios, cloudkit, api, sync
published: true
---

### I need servers

At one point or another you will have an idea for application that will also need a backend. I have personally gone through multiple phases playing with [Spring framework][1], [Ruby on Rails][2] and [Laravel][3]. These were only mainstream frameworks. But as I live and breathe iOS daily, none of these ever clicked with me. Same thing happened always - after playing with these a bit, other responsibilities come up and you forget server side development.

Now when Swift is taking over Apple world, Swift on server side seems really appealing. Using same technologies on frontend and backend side seems really appealing. You could potentially also share logic between these. Last year I was ready to start exploring server side Swift when WWDC2016 happened...

### Move me to cloud

Somehow I previously had not thought about using iCloud. I'm assuming, that the main reason was, that previously there was no way to share user records. Everything was either public or user private. This all changed with iOS 10, that introduced sharing of private iCloud records. That was the final hurdle for me, to take a serious look.

There was additional use case, where I was thinking that custom backend was needed - when you wish to share same data between different kind of apps. Then I realized, that you can share iCloud containers between applications from same company 😍

Possible drawback of iCloud is, that you can't execute server side logic. But depending on your needs there are alternatives. You could subscribe to data update on app side. Or user server-to-server API for periodic data updates/checks.

For server-to-server requests I created my own nifty library [TakToCloud][10].

### All bases covered

These are the cases where I'm currently using CloudKit.

#### Beers
In [Gambrinus app][4] I'm using app specific container to map blog posts to [RateBeer][5] scores. Rate beer information is contained in a shared container, that is also used in beers log prototype I'm working on.

RateBeer score updates are pushed to shared container from command line.

#### Feedback
[LaughigAdventure][6] contains shared code to gather user feedback. This can be included in any app. Conversations and Messages will be created in public database. On client side only Conversations started by current user are shown.

Currently it's included in [Find movies to watch][7] and [TenPair][9].

#### Movies
In [Find movies to watch][7] I'm saving iTunes matches movies from [The Movie DB][8] to iCloud container. Locally I'm using server-to-server API to check old matches for updates.

#### Budget prototype
I'm record expenses in private user database with option to share these with other users. iCloud is used just as data bucket. All logic for summaries is on app side.

### Conclusion

As I'm currently concentrating only on Apple platforms, I don't see any need to look beyond iCloud for server needs. It may require some compromises. You can't use it for public API-s, but overall my current needs are covered.

[1]: https://projects.spring.io/spring-framework/
[2]: http://rubyonrails.org
[3]: https://laravel.com
[4]: https://github.com/coodly/ios-gambrinus
[5]: https://www.ratebeer.com
[6]: https://github.com/coodly/laughing-adventure
[7]: https://itunes.apple.com/app/find-movies-to-watch/id1107657424?mt=8&at=1000lmKH
[8]: https://www.themoviedb.org
[9]: https://itunes.apple.com/app/tenpair-the-game-of-numbers/id837173458?mt=8&at=1000lmKH
[10]: https://github.com/coodly/TalkToCloud