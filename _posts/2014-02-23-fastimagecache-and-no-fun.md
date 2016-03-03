---
layout: rock
title: FastImageCache and no fun
tags:
- ios, fastimagecache, gotcha
published: true
---

**Why**

Working on personal pet project - Beers (I need to write about it!), I have made some use
of [FastImageCache][1] created by Path. Maybe it's not best suited for current project, 
but does the work for now (I have to write about this too!).

This project has an admin module. As an iOS developer, ofcourse this needs to be also done
on iOS. Specifically on iPad.

**The problem**

After laying out all the code for API communication and pulling/caching images from
Amazon S3 I used FastImageCache to present pulled images. 

Everything was set up by instructions

{% highlight objc %}
    FICImageFormat *imagesListThumbnail = [[FICImageFormat alloc] init];
    imagesListThumbnail.name = BeerImageListThumbnail;
    imagesListThumbnail.family = BeerImageFamilyName;
    imagesListThumbnail.style = FICImageFormatStyle16BitBGR;
    imagesListThumbnail.imageSize = CGSizeMake(176, 176);
    imagesListThumbnail.maximumCount = 250;
    imagesListThumbnail.devices = FICImageFormatDevicePhone;

    BeerImagesRetrieve *imagesRetrieve = [[BeerImagesRetrieve alloc] init];
    [self setImagesRetrieve:imagesRetrieve];

    FICImageCache *sharedImageCache = [FICImageCache sharedImageCache];
    [sharedImageCache setDelegate:imagesRetrieve];
    [sharedImageCache setFormats:@[imagesListThumbnail]];
{% endhighlight %}

And callback in view controller:

{% highlight objc %}
    FICImageCacheCompletionBlock completionBlock = ^(id <FICEntity> entity, NSString *formatName, UIImage *image) {
        imageView.image = image;
        [imageView.layer addAnimation:[CATransition animation] forKey:kCATransition];
    };

    [[FICImageCache sharedImageCache] retrieveImageForEntity:identifier withFormatName:BeerImageListThumbnail completionBlock:completionBlock];
{% endhighlight %}

But images were just not appearing. Cache completion block for image hit was just not called. 
Everything was executing perfectly until my network call was hitting cache callback. 
Then it just disappeared.

No matter what I tried, I coud not figure out where it goes.

When you can't figure something out, then it needs to be deleted and started from scratch :)

**The gotcha**

After rewriting image pull part without additional caching besides FastImageCache, all was
wired up again, but my callback was not called |-(

Thank god for open source. Time to dive into FastImageCache source. After some poking around
I ended up at this piece of code:

{% highlight objc %}
        FICImageFormatDevices currentDevice = [[UIDevice currentDevice] userInterfaceIdiom] == UIUserInterfaceIdiomPad ? FICImageFormatDevicePad : FICImageFormatDevicePhone;
        for (FICImageFormat *imageFormat in formats) {
        
			....
        
            if (devices & currentDevice) {
                // Only initialize an image table for this format if it is needed on the current device.
				.....
            }
        }
{% endhighlight %}

Problem was in my setup:

{% highlight objc %}
	...
    imagesListThumbnail.devices = FICImageFormatDevicePhone;
	...
{% endhighlight %}

Image format was defined only for phone. When executing admin app on iPad no images
table was created and all cache hit attempts were ignored...

This was four hours well spent :)


[1]: https://github.com/path/FastImageCache