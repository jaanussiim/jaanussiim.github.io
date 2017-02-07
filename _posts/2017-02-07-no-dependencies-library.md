---
layout: rock
title: No dependencies library
tags:
- ios, swift, dependency, design, library
published: true
---

### The in

We all have done it. When working on something you find a piece of code that could be extracted into a separate library. Lets call it **furry-guide**. **Furry-guide** could also be useful for other developers and as a good open source contributor you upload it to GitHub. You also make sure that it can be used with popular dependency managers.

As you were developing **furry-guide** in parallel with the main app, you included some of your favorites libraries as dependency to it. Maybe you just wanted to used that awesome logging framework you use everywhere. Or you needed some networking functionality and marked [Alamofire][1] version 4.1.0 as dependency for **furry-guide**.

Overall this should not be an issue. These dependencies are you go to helpers. And in every project you will be using **furry-guide**, they will be included anyway. So there is no real overhead. It's just convenient.

By defining these dependencies to **furry-guide**, you have created a small hurdle for using it. Maybe I'm using different logging library and now **furry-guide** will pull in extra one not used anywhere else. Maybe I need a different version of Alamofire? Or I'm using URLSession everywhere.

When working on [Find movies to watch][2], I decided to create some components as separate libraries. [iTunesSearch][3] for finding movies on iTunes. [TheMovieDatabase][4] for getting movies lists. [ImageProvide][5] for posters fetching and caching. All these needed two common functionality: logging and network. 

For images fetching  and caching I have an earlier attempt [CDYImagesRetrieve][6], that declared AFNetworking as dependency. And it had preprocessing macro for enabling logging. Now I wanted to do it better.

### The out
Solution that I came up with was to define protocols for these, that app can inject. For example app defines logging protocol:

{% highlight swift %}
public protocol Logger {
    func log<T>(_ object: T, file: String, function: String, line: Int)
}
{% endhighlight %}

On app side you need to create a logger:

{% highlight swift %}
private class ImageLogger: ImageProvide.Logger {
    fileprivate func log<T>(_ object: T, file: String, function: String, line: Int) {
    	// log the message to app logger
    }
}
{% endhighlight %}

And inject. Only if you need it.
{% highlight swift %}
ImageProvide.Logging.set(logger: ImageLogger())
{% endhighlight %}

This gives the control over logging to application developer. Developer decides if library logging should be enabled and where the output goes.

Same pattern is used for networking. Library defines a protocol:
{% highlight swift %}
public protocol NetworkFetch {
    func fetch(request: URLRequest, completion: @escaping (Data?, URLResponse?, Error?) -> ())
}
{% endhighlight %}

On app side you define library requests handler:

{% highlight swift %}
import TheMovieDatabase

class TMDBFetch: NetworkFetch {
    private let queue: NetworkQueue
    init(queue: NetworkQueue) {
        self.queue = queue
    }
    
    func fetch(request: URLRequest, completion: @escaping (Data?, URLResponse?, Error?) -> ()) {
        queue.append(request, priority: .high, completion: completion)
    }
}
{% endhighlight %}

And inject it into API client:

{% highlight swift %}
let fetch = TMDBFetch()
let tmdb = TMDB(apiKey: TheMovieDBAPIKey, networkFetch: fetch)
{% endhighlight %}

This gives application developer control over how these requests should be made. Additionally it allows developer to prioritize app network calls over library network calls.

### Conclusion

Give power to developers 🤘

[1]: https://github.com/Alamofire/Alamofire
[2]: https://itunes.apple.com/app/find-movies-to-watch/id1107657424?mt=8&at=1000lmKH
[3]: https://github.com/coodly/iTunesSearch
[4]: https://github.com/coodly/TheMovieDatabase
[5]: https://github.com/coodly/ImageProvide
[6]: https://github.com/coodly/CDYImagesRetrieve