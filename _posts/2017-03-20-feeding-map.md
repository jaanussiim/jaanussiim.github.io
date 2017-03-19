---
layout: rock
title: Feeding pins to map
tags:
- ios, map, pins, core data, poi
published: true
---

### Coming in

I started iOS development with iPhoneOS version 3. In that version Core Data was also introduced to mobile platforms. And to be honest, I have really never used anything else for persistence. It may not be perfect, but you have to love it just for automated update callback from NSFetchedResultsController. And who tells that you should use it only to feed tables and collection views? I have used it everywhere where data changes needed to be tracked. Syncing, triggering data refreshes, handling markers on map 🤓

### The model

In example model we are showing two types of markers on map. Players and Places. They both have one to one relationship with PlaceOfInterest. PlaceOfInterest entity is the one that is used for showing pins on map.

![Core Data model](/images/pins/PinsModel.png)

When ever places or payers are added or removed. Or when players location is updated. These events will trigger create/delete/modify for attached PlaceOfInterest entity.

If we create NSFetchedResultsController for PlaceOfInteres, we will be notified about all these changes through NSFetchedResultsControllerDelegate.

{% highlight swift %}
    func controller(_ controller: NSFetchedResultsController<NSFetchRequestResult>, didChange anObject: Any, at indexPath: IndexPath?, for type: NSFetchedResultsChangeType, newIndexPath: IndexPath?) {
        
        guard let poi = anObject as? PointOfInterest else {
            return
        }
        
        if type == .insert {
            // add new pin to map
            return
        }

        let marker = ... // get marker that is attached to this poi
        
        if type == .delete {
            // we are using Google maps
            marker.map = nil
            return
        }
        
        // this was .update event. Update marker coordinate
        marker.position = poi.coordinate
    }
{% endhighlight %}

With this logic in place we can easily add additional pin types. Refreshing data somewhere in the background, just updating on it's own and letting core data feed everything to map. As long as we keep model data in sync, we don't need to make additional checks for showing new markers and hiding old ones.

### Conclusion

With all it's problems Core Data is still a really neat framework. When ever I need to be kept in loop about data changes, I'll be using NSFetchedResultsController to track these. No manual data checks for me!