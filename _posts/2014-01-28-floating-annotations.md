---
layout: rock
title: Floating annotations
tags:
- ios, map, annotation, mapkit
published: true
---

**Idea**

When working on yet another location app I wanted to try out the pin float method used
in Apple Maps app. Press and hold pin. See it hover. Move and drop it. Digging through
documentation I could not any inbuilt method to achieve this...

**Execution**

Let's dig in - MKAnnotationView is extending UIView. This means I can show there anything?
Simple rect with image in center should be enough to test the theory.

Standard code to show annotation view on map:

{% highlight objc %}
- (MKAnnotationView *)mapView:(MKMapView *)mapView 
            viewForAnnotation:(id <MKAnnotation>)annotation {
    FloatingAnnotationView *annotationView 
        = (FloatingAnnotationView *) [mapView dequeueReusableAnnotationViewWithIdentifier:@"identifier"];
    if (!annotationView) {
        UIImage *pin = [UIImage imageNamed:@"Pin"];
        annotationView = [[FloatingAnnotationView alloc] initWithAnnotation:annotation 
                                                           reuseIdentifier:@"identifier" 
                                                                     image:pin];
    }

    return annotationView;
}
{% endhighlight %}

And here is custom annotation we will be using:

{% highlight objc %}
@implementation FloatingAnnotationView

- (id)initWithAnnotation:(id <MKAnnotation>)annotation 
         reuseIdentifier:(NSString *)reuseIdentifier 
                   image:(UIImage *)image {
    self = [super initWithAnnotation:annotation reuseIdentifier:reuseIdentifier];
    if (self) {
        [self setBackgroundColor:[UIColor redColor]];
        CGRect frame = CGRectMake(0, 0, image.size.width, image.size.height);
        [self setFrame:frame];
        [self setCenterOffset:CGPointMake(0, -CGRectGetHeight(frame) / 2)];
        UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
        [self setImageView:imageView];
        [self addSubview:imageView];
    }

    return self;
}

@end
{% endhighlight %}

![Custom pin on map](/images/lifter/Lifter-shot-001.png)

Success!!!

Making the pin draggable is just one line of code

{% highlight objc %}
....
        [self setDraggable:YES];
....        
{% endhighlight %}

Now to make it 'hover'... First we need to figure out in what dragging state pin currently
is. For this we need to override 'setDragState' in our custom annotation view.

{% highlight objc %}
- (void)setDragState:(MKAnnotationViewDragState)newDragState animated:(BOOL)animated
{% endhighlight %}

Let's also add some room for annotation image to 'float' in. And we position image at
the bottom of annotation.

{% highlight objc %}
...
        CGRect frame = CGRectMake(0, 0, image.size.width, image.size.height * 2);

        CGRect imageFrame = imageView.frame;
        imageFrame.origin.x = (CGRectGetWidth(frame) - CGRectGetWidth(imageFrame)) / 2;
        imageFrame.origin.y = CGRectGetHeight(frame) - CGRectGetHeight(imageFrame);
        [imageView setFrame:imageFrame];
...
{% endhighlight %}

Here comes the magic:

{% highlight objc %}
...
- (void)setDragState:(MKAnnotationViewDragState)newDragState animated:(BOOL)animated {
    [super setDragState:newDragState animated:animated];

    if (newDragState == MKAnnotationViewDragStateStarting) {
        [UIView animateWithDuration:0.3 animations:^{
            CGRect imageFrame = self.imageView.frame;
            imageFrame.origin.y = 0;
            [self.imageView setFrame:imageFrame];
        }];
    } else if (newDragState == MKAnnotationViewDragStateNone) {
        [UIView animateWithDuration:0.3 animations:^{
            CGRect imageFrame = self.imageView.frame;
            imageFrame.origin.y = CGRectGetHeight(self.frame) - CGRectGetHeight(imageFrame);
            [self.imageView setFrame:imageFrame];
        }];
    }
}
...
{% endhighlight %}

When annotation dragging is started (Line 5), then image is animated
to the top of annotation view. When dragging has ended (Line 11), then
image is moved back to bottom of animation view. Thus giving the illusion of floating.

Note: there is some code in view controller to reset annotation dragging state. Otherwise
animations would have stayed in odd state.

{% highlight objc %}
...
- (void)mapView:(MKMapView *)mapView annotationView:(MKAnnotationView *)view 
                                 didChangeDragState:(MKAnnotationViewDragState)newState 
                                       fromOldState:(MKAnnotationViewDragState)oldState {
    if (newState == MKAnnotationViewDragStateCanceling || newState == MKAnnotationViewDragStateEnding) {
        [view setDragState:MKAnnotationViewDragStateNone animated:YES];
    }
}
...
{% endhighlight %}

Here we have the effect animated:

![Animated pin on map](/images/lifter/Lifter.gif)

So there actually is no magic...


**Where to go from here**

* To improve tap detection, annotation view should be made also wider.
* When lifting pin under the users finger, it would be better to use top position of drag
end for new annotation location, instead of moving it back under user finger. That would
allow for more precise location selection. And would avoid multiple failed move attempts :)

All code is available on [GitHub][1].

[1]: https://github.com/jaanussiim/ios-demo-lifter