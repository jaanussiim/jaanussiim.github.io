---
layout: rock
title: Making 'Find movies to watch'
tags:
- app, movies, iOS, tvOS, tmdb, itunes, iCloud
published: true
---

**The why**

After purchasing TV, iTunes and Netflix have been by go to solutions for watching movies. But I always felt that something was missing in discovering what was available. iTunes presents what is currently popular and Netflix has rather limited availability in Estonia. 

What I was lacking was larger list of movies by rating or popularity. Also browsing movies by genre. Something like IMDB? It covers the list, but finding something interesting there, going to iTunes and performing search was not that much fun. Other thing I did was finding movies on iTunes and trying to match scores for these in IMDB. Performing manual lookups was pain both ways.

**The where**

There is no problem that can't be solved by an app™. Just some inputs, a bit of magic and we would be done. First we would need a list of movies. Check that they are available on iTunes. Save matches somewhere to make future lookups faster. Matches need to also be checked after some period because movies will become available and disappear.

*Movies lists*

Unfortunately IMDB does not have an official API. Luckily there is a good alternative with public API providing various lists, ratings and details - [The Movie Database][1]. I decided to encapsulate TMDB API access into separate library that could also increase my open source cred: [TheMovieDatabase][2]. Of course it's limited to functionality that was needed for my app.

*iTunes matches*

For searching iTunes there is [iTunes affiliate][3] program. Joining it was rather easy. They provide country based search API for titles. Accessing it provided opportunity for another open source library: [iTunesSearch][4]. Providing functionality needed by the app.

*Hits cache*

With iOS 10 CloudKit got a lot more interesting for me. Mainly because of record sharing, that I wish to use in another project. Here I wanted to save TMDB movie matches from iTunes. Just to make these available to all users. And also offload some movies matching tasks into app.

Original idea was to take movie info from TMDB, perform search in iTunes and save results to iCloud. Next user who comes along will get faster match results by querying iCloud first and performing searches only when not cached.

**Putting it together**

First version was targeting only tvOS. All the matches were done in app. It did not follow tvOS interface guidelines. It was showing only popular list. It looked like this

![tvOS screenshot v1.2](/images/movies/tvOS-version-1.1.png)

It was slow as hell. It took forever to get somewhere in the list. Something odd was also going on with matches - not all movies available in iTunes were shown in the app.

**Taking it apart**

Something that really did not work was matching on device. Data from TMDB and iTunes often did not match. There were multiple reasons. Release year may be off, because iTunes uses (sometimes) dates when movies were added to it. Small differences in movie titles. As far as I could tell, automatic matching was not possible by me. It was obvious that matching needed some more hands on approach.

**Putting it together. Again...**

For next attempt it was obvious that iOS should also be supported for this app to go anywhere. When doing app redesign, tvOS interface guidelines were also checked. The bigger work was in getting matches right.

Best solution I could come up with - somebody had to got through TMDB list and manually mark down matching results on iTunes. Lots of manual work... But for proof of concept... Feeding the database with top rated lists for USA, Canada, Australia and Great Britain seemed manageable. 

With design updates I was ready to see how manageable it really was...

tvOS version
![tvOS screenshot v1.3](/images/movies/tvOS-version-1.3.png)

iOS version

![iOS screenshot v1.3](/images/movies/iOS-version-1.3.png)

It was still just a list of top rated movies, but an app started to emerge from it. If movie was available, you were taken to iTunes Store. If it was not available, appropriate notice was shown.

**Evaluating the concept**

My main concern was how the movies matching would work and scale. A short list was prepared for country. When user goes beyond that list, markers are created in iCloud for checking. Early users would often hit items that were not yet checked. But at the same time it was extending hits database in regions with active users. Overall the idea seemed to be working and I could release the app to all countries where iTunes movies is available. And I could extend the app by adding lists for popular movies and movie genres. Also the checks were growing in manageable speed, but there were some simple improvements that could be made.

**Automated checks**

One pattern that quickly emerged with manual checking - searching iTunes with movie name had no results, mark check as unavailable. 

After I had few thousand verified checks, I started to wonder, if these could be used for automated checks. Investigation revealed, that sometimes iTunes movie ids were shared between different regions. This meant that potentially some hits could be marked automatically based on existing verified results. Resulting in less manual work.

In 2015 Apple introduced [CloudKit Web Services][5]. This exposes JSON API to CloudKit containers where I'm saving search hit markers. After creating Swift library [TalkToCloud][6] for CloudKit server-to-server communication I could create small command line tools for perform these checks periodically.

**Conclusion**

On move nights this app has become first stop for me. Usually I find something that matches my mood. Sometimes these are not available in Estonia, but there is nothing I can do about that. As I continue dogfooding, the app will be improved. Next idea - local list of interesting movies.

Overall this project has created multiple interesting puzzles to solve. Using CloudKit from app and command line. Thinking about library design. Exploring tvOS. Code reuse between iOS and tvOS.

<a href="https://itunes.apple.com/app/find-movies-to-watch/id1107657424?mt=8&at=1000lmKH" style="display:inline-block;overflow:hidden;background:url(//linkmaker.itunes.apple.com/assets/shared/badges/en-us/appstore-lrg.svg) no-repeat;width:135px;height:40px;background-size:contain;"></a>

[1]: https://www.themoviedb.org
[2]: https://github.com/coodly/TheMovieDatabase
[3]: http://www.apple.com/itunes/affiliates/
[4]: https://github.com/coodly/iTunesSearch
[5]: https://developer.apple.com/library/content/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html
[6]: https://github.com/coodly/TalkToCloud