---
layout: rock
title: One shared instance to rule them all
tags:
- ios, programming, injection
published: true
---

### I share, you share....

I was re-watching [WWDC16][2] video [Core Location Best Practices][1] and there were two phrases that stuck with me.

"You might also consider attaching it to your app delegate instead".

"You might want to attach it to your app delegate".

There is a bad patter of global shared instances usage in Apple world. And I believe this is caused by two reasons. Sample code from Apple is intended as demonstration and it's easy to dump everything into app delegate. Because it's so convenient to access variables from there. Just call:

{% highlight swift %}
UIApplication.shared.delegate.myProperty
{% endhighlight %}

The other culprit is storyboards. Maybe it's just me, but I have never gotten along with these. You always try to use these in new project, but after some fighting you just give up and revert to old mode. One reason for fighting is because they unintentionally seem to force you to use shared instance pattern. Without shared instances juggling dependencies becomes just too cumbersome.

And as unit testing is not that common in iOS, you don't really see the pain when using shared instances.

### First light

A long time ago I came to a pattern where all dependencies were given to created object. For example core data wrapper. You create common instance in AppDelegate and forward it to root controller. That one will forward it to created/presented children. 

Code wise it felt cleaner, but was also not perfect. Forwarding multiple dependencies became also awkward. And in good old Objective-C days it created fun bugs. When you forgot to forward something and nothing was happening because there are no NullPointerExceptions.

### The one

With Swift and protocol extensions stumbled onto something I feel really happy with. I call it 'injector pattern'.

First you create protocols for dependency requirements. Samples from [Find movies to watch][3]:

{% highlight swift %}
protocol ImagesConsumer {
    var imageSource: ImageSource! { get set }
}

protocol PersistenceConsumer {
    var persistence: CorePersistence! { get set }
}

protocol TMDBConsumer {
    var tmdb: TMDB! { get set }
}

{% endhighlight %}

Classes needing shared resources will conform to these protocols. For injection logic we have protocol with default implementation:

{% highlight swift %}
protocol InjectionHandler {
    func inject(into: AnyObject)
}

extension InjectionHandler {
    func inject(into: AnyObject) {
        Injector.sharedInstance.inject(into: into)
    }
}

private class Injector {
    static let sharedInstance = Injector()

    private lazy var tmdb: TMDB = //create TMDB client
    private lazy var persistence: CorePersistence = // create core data wrapper

    func inject(into: AnyObject) {
        if var consumer = into as? PersistenceConsumer {
            consumer.persistence = persistence
        }

        if var consumer = into as? TMDBConsumer {
            consumer.tmdb = tmdb
        }
    }
}
{% endhighlight %}

Now when controllers push other controller that need dependencies, they will implement *InjectionHandler* and can perform the injection:

{% highlight swift %}
extension MoviesViewController: InjectionHandler {
    func present(movie: Movie) {
        let details = MovieDetailsViewController()
        inject(into: details)
        details.movie = movie
        navigationController?.pushViewController(details, animated: true)
    }
}
{% endhighlight %}

Because of the ! in swift, you will find out very quickly if dependencies injection was not done.

### Conclusion

Maybe it's not perfect, but it makes working with dependencies a lot cleaner. Controllers still know what they are presenting, but at least not too many details are leaked. Also working with injected dependencies, unit testing becomes simpler.

[1]: https://developer.apple.com/videos/play/wwdc2016/716/
[2]: https://developer.apple.com/videos/
[3]: https://itunes.apple.com/app/find-movies-to-watch/id1107657424?mt=8&at=1000lmKH