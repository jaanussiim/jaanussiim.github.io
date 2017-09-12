---
layout: rock
title: The perfect callback
tags:
- ios, network, threading, ui, background
published: true
---

When ever you need a callback from a task, but just sometimes need to get it back to UI thread. Specify during execution on what thread callback should be called.

Base task for handling callbacks on specified queue:

{% highlight swift %} 
open class AsyncTask<Result> {
    private var callbackOn: DispatchQueue!
    internal var result: Result? {
        didSet {
            guard let result = result else {
                return
            }
            
            callbackOn.async {
                self.resultHandler?(result)
            }
        }
    }
    public var resultHandler: ((Result) -> Void)?
    
    // will used background queue for callback if not otherwise specified on call site.
    public func execute(callbackOn queue: DispatchQueue = DispatchQueue.global(qos: .background)) {
        callbackOn = queue
        perform()
    }
    
    open func perform() {
    }
}
{% endhighlight %}

Sample task for fetching user details:

{% highlight swift %} 
struct User {
    
}

class UserDetails: AsyncTask<User> {
    override func perform() {
        // perform async work
        
        result = User()
    }
}
{% endhighlight %}

Sample usage:

{% highlight swift %} 
let detailsTask = UserDetails()
detailsTask.resultHandler = {
    user in
    
    // Update UI
}
detailsTask.execute(callbackOn: DispatchQueue.main)
{% endhighlight %}
