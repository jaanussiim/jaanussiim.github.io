---
layout: rock
title: No profile left behind
tags:
- ios, programming, design
published: true
---

### Adding details

Lates version of [Find movies to watch][1] introduced movie details presentation. This also includes actor profile pictures.

![Find movies details page sample](/images/profile/Profiles.png)

These images are based on info from [The Movie Database][3]. When profile is show we do lazy loading of image using [ImageProvide][2]. This means either loading from local cache and presenting. Or retrieving it from remote source, caching for future use, and presenting.

But in some cases actor profile is missing image and I wanted to handle it by showing generated image with actor initials. Same way how [The Movie Database][3] does it. All this should ideally be done without changing existing presentation logic. I did not want to add additional code for handling generated images caching and presentation.

### The interception

First step in achieving it was to introduced different profile image URL for missing ones. When TMDB was missing image, then actor object returned custom URL with following format:

{% highlight plain %}
profile://image/JT
{% endhighlight %}

Last characters are for actor name initials to be used in generated image.

[ImageProvide][2] is using the fetcher pattern where it requires that app injects logic for retrieving data from network. This gave me a perfect interception point for handling missing images.

{% highlight swift %}
import ImageProvide

class ImageFetch: RemoteFetch {
    func fetchImage(for ask: ImageAsk, completion: @escaping (Data?, URLResponse?, Error?) -> ()) {
        // ImageAsk contains requested URL
        guard /* check for profile scheme */ else {
            // not generated profile image URL, make remote request
            // .......
            return
        }
        
        DispatchQueue.global(qos: .background).async {
            // create image with initials
            // .......
            // finally return image data for caching and presenting in UI
            let data = UIImagePNGRepresentation(image)
            completion(data, nil, nil)
        }
    }
}
{% endhighlight %}

With minimal changes outside presentation logic I was able to leverage the usual pull/cache/present logic that was already available for other images.

![Find movies profile intials](/images/profile/Filled.png)

## Conclusion

When time permits, I do my best to come up with simplest and most elegant solution possible. By adding some code outside main application logic I was able to introduce new feature with minimal effort. 

I may not always get there, but this time I do feel like I nailed it :)

[1]: https://itunes.apple.com/app/find-movies-to-watch/id1107657424?mt=8&at=1000lmKH
[2]: https://github.com/coodly/ImageProvide
[3]: https://www.themoviedb.org