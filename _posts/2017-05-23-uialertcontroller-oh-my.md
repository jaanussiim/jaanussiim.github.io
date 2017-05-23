---
layout: rock
title: UIAlertController, oh my....
tags:
- ios, ui, modal, custom
published: true
---

### All goes modal

With the introduction of UIAlertController in iOS8 a new era began where you could present modal controllers partially covering presenting controllers. But as with everything, it also brought new problems. Every view controller can only present one modal controller. And it becomes tricky when you need to show multiple alerts or messages with custom presentation at the same time.

### Queue the modal pieces

We can start with single service for managing all the alerts and custom popups. This service will present all queued messages using app root controller.

{% highlight swift %}
class AppMessagesHandler: NSObject {
    weak var presentOn: UIViewController?
    private var controllers = [UIViewController]()
    
    func present(alert: UIAlertController) {
        present(popup: alert)
    }
    
    func presentNext() {
        guard self.controllers.count > 0 else {
            return
        }
            
        guard self.presentOn?.presentedViewController == nil else {
            return
        }
            
        let show = self.controllers.removeFirst()
        self.presentOn?.present(show, animated: true)
    }
    
    func present(popup controller: UIViewController) {
        controller.modalPresentationStyle = .custom
        self.controllers.append(controller)
        self.presentNext()
    }
}
{% endhighlight %}

If nothing is shown, present the modal controller. But what happens, when there is existing alert and you need to queue next message? This is put into *controllers* array and then...?

### Detect the dismiss

Obvious way is to call *presentNext()* in root controller every time when *viewDidAppear()* is called. But these event is only called when controller is added to view hierarchy. With modal popups root controllers view is not removed from view hierarchy... It stays visible under the presented popup...

So we need another method for pushing modal popups presentation. There is one view were every controller view will end up. Actually it's a window.

{% highlight swift %}
class AppWindow: UIWindow {
    var messagesHandler: AppMessagesHandler!
    
    override func willRemoveSubview(_ subview: UIView) {
        super.willRemoveSubview(subview)
        
        messagesHandler.presentNext()
    }
}
{% endhighlight %}

By setting custom window as application window in app delegate, we can monitor for changes in view hierarchy. And when ever something is remove, try to present queued alert.
