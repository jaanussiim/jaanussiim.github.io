---
layout: rock
title: Driving UI using Core Data
tags:
- ios, ui, core data
published: true
---

### Going deeper

For last week I have explored more ways how to drive UI state using core data. What I wanted to show some additional detail views based state in a hypothetical game. Game play can be in following states: 

* no game
* matching
* active
* post mortem

I needed to include opponent information to two screens. On map screen only in active mode. And on chat screen during matching and active play. Game status is updated using active network polling in background. All the game status updates are saved using core data.

In UI map and chat views are using stack views. Main view on top and accessory view in bottom. When accessory is not shown, main view will be full screen. There is additional catch - players can be in two modes. They can be either attackers or defenders. Both modes will use different accessory view. At launch we insert three views into stack view - main view, attacker view, defender view. Last two ones are hidden and only relevant one will be shown when needed. At most two views will be shown simultaneously.

Showing player status will be contained in AttackerStatusViewController and DefenderStatusViewController. When needed, views will be made visible. When not needed, views will be hidden. And stack views will do all the magic 😍

### The monitors

For monitoring database changes, we have a simple wrapper with callback around fetched results controller:

{% highlight swift %}
import CoreData

class CoreDataMonitor: NSObject, NSFetchedResultsControllerDelegate {
    private let monitored: [CoreDataMonitored]
    private let callback: (() -> ())
    
    init(controller: CoreDataMonitored, callback: @escaping (() ->())) {
        self.monitored = [controller]
        self.callback = callback

        super.init()
        
        controller.delegate = self
    }

    init(controllers: [CoreDataMonitored], callback: @escaping (() ->())) {
        self.monitored = controllers
        self.callback = callback
        
        super.init()
        
        for controller in controllers {
            controller.delegate = self
        }
    }

    func controllerDidChangeContent(_ controller: NSFetchedResultsController<NSFetchRequestResult>) {
        callback()
    }
}
{% endhighlight %}

In AttackerStatusViewController/DefenderStatusViewController we instantiate the monitor:

{% highlight swift %}
let monitored = mainContext.fetchedControllerForAllGames()
gamesMonitor = CoreDataMonitor(controllers: monitored) {
    [weak self] in
    Log.debug("Games changed")
            
    self?.updateUI()
}
{% endhighlight %}

When ever update callback is received, we can show/hide presenting view and fill in other details:

{% highlight swift %}
private func updateUI() {
    guard let game = mainContext.activeGame(), let user = mainContext.me() else {
        view.isHidden = true
        return
    }
        
    guard game.status == .active else {
        view.isHidden = true
        return
    }

    guard user.status == .attacker else {
        view.isHidden = true
        return
    }

    view.isHidden = false

    // fill in player and game info
}
{% endhighlight %}

### Conclusion

I'm still evaluating if this is a good idea. But I do like the decoupling here. Game status updates are monitored somewhere far away from UI and saved using core data. When something changes, this will be notified back to UI level where appropriate steps can be taken.