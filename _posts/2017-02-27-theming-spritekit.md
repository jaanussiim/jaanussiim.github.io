---
layout: rock
title: Theming SpriteKit
tags:
- ios, spritekit
published: true
---

### Dirty singletons

When I first started working on [TenPair][1] I knew that I should also do something about game UI themes support. But I did not have clear idea how to approach it at that time. This resulted in following monstrosity:

{% highlight swift %}
 background.fillColor = TenPairTheme.currentTheme.consumedTileColor!
{% endhighlight %}

Well... At least colors were not hardcoded... But it wasn't also much better. Dirty singletons everywhere... And I never got to how theme changes should be handled.

With the latest update to TenPair I wished to add at least one additional color theme. And as it was period for rethinking [SpriteKitUI][2], it was time to address theming.

### SpriteKit appearance?

UIKit has a nice way to set elements appearance app wide. For example setting navigation bar color:

{% highlight swift %}
UINavigationBar.appearance().barTintColor = .blue
UINavigationBar.appearance().tintColor = .white
{% endhighlight %}

Set once and forget. I wished to use similar pattern.

### Theming

First step for identifying elements that can be themed was to introduce theming protocol

{% highlight swift %}
public protocol Themed {
    static func appearance() -> Appearance
    
    func set(color: SKColor, for attribute: Appearance.Attribute)
    func set(value: String, for attribute: Appearance.Attribute)
}
{% endhighlight  %}

*Appearance* defines a bag of properties for every UI element based on full class name. *Appearance.Attribute* is user defined property key that will be used when theme is applied.

Sample usage for defining app wide View colors: 

{% highlight swift %}
View.appearance().set(color: SKColor.white, for: .background)
View.appearance().set(color: lightBlue, for: .foreground)
{% endhighlight %}

During theming phase elements will receive defined properties with attribute keys:
{% highlight swift %}
open class View: SKSpriteNode, Themed
    open func set(color: SKColor, for attribute: Appearance.Attribute) {
        switch attribute {
        case Appearance.Attribute.background:
            backgroundColor = color
        default:
            break // no op
        }
    }
}
{% endhighlight %}

When ever colors need to be changed, you will need to perform appearance calls with new values and call applyTheme() on game object

![Theme demo](/images/themed/Themed.gif)

### Conclusion

Overall this allowed clear separation of theme definition form game logic and UI. If I only had more theme ideas...

<a href="https://itunes.apple.com/app/tenpair-the-game-of-numbers/id837173458?mt=8&at=1000lmKH" style="display:inline-block;overflow:hidden;background:url(//linkmaker.itunes.apple.com/assets/shared/badges/en-us/appstore-lrg.svg) no-repeat;width:135px;height:40px;background-size:contain;"></a>

[1]: https://github.com/coodly/ios-ten-pair
[2]: https://github.com/coodly/SpriteKitUI