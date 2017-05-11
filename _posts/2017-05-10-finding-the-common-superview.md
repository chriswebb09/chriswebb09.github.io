---
layout: post
title: Finding The Common Superview 
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

![View Hierachy](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/hit-test-view-hierarchy.png)

[Gist](https://gist.github.com/chriswebb09/1e4e5a1aa5f2e6fa3896c0a28975c5e7)

### Brief Introduction To The View Hierarchy In iOS

When programming your iOS application, one thing you will need to be aware of is the view hierarchy. At its most basic level, the view hierarchy encompasses the parent-child relationships between different views in your application. How you structure this will determine how your application looks and how you can interact with it.

#### Apple-y Definition: 

_When one view contains another, a parent-child relationship is created between the two views. The child view in the relationship is known as the subview and the parent view is known as the superview. The creation of this type of relationship has implications for both the visual appearance of your application and the application’s behavior._

##### Superviews

A superview is a view that is higher on the view tree than it’s subviews. Another way to put this is that a superview is the parent view of a subview. Being higher on the view tree means it is closer to the rootview.

##### Subviews 

Subviews are the child views of a parent and are further down the view hierarchy. In fact you can check for a view's subviews by accessing the subviews property which will return an array of views that are subviews if there are any subviews.

### Getting Started

Sometimes it is not immediately obvious which view is the superview. So how would we go about finding the common superview for two views? To begin, lets focus on the first input view. For the sake of this exercise I'm going to assume that each view has been given a unique integer for its tag property. We can create a dictionary and use this tag as the key for each value which will be it's view. 

{% highlight swift linenos %}

class ViewTraverser {
    func traverseSuperViews(view: UIView) -> [Int : UIView] {
        var views: [Int: UIView] = [:]
        var inputView: UIView? = view
        while inputView != nil {
            guard let tag = inputView?.tag, let view = inputView else { continue }
            views[tag] = view
            inputView = view.superview
        }
        return views
    }
}

{% endhighlight %}

The dictionary will should have a the view hierarchy stored in key value pairing with each view tag serving as the key for the view. We can now check the superviews of our second view using the tags of its superviews to lookup the view in the dictionary. Lets create a method that takes in a view uses the tag of that view as a key to check if there is a corresponding view in the dictionary. If the view exists, we return it. 

{% highlight swift linenos %}

class ViewTraverser {

  // Traverse superviews 
  
    func checkForSuper(view: UIView?, views: [Int: UIView]) -> UIView? {
        guard let view = view else { return nil }
        if views[view.tag] != nil {
            return view
        }
        return nil
    }
}

{% endhighlight %}

Let's combine this functionality into a method that takes in two views and returns an optional view. The return value should be optional because there may not be a common superview. 

{% highlight swift linenos %}

class ViewTraverser {

  // Traverse superviews
  
  // Check for superview 
  
     func commonSuper(viewOne: UIView, viewTwo: UIView) -> UIView? {
        var inputTwo: UIView? = viewTwo
        let superViews: [Int: UIView] = traverseSuperViews(view: viewOne)
        while inputTwo != nil {
            if checkForSuper(view: inputTwo, views: superViews) != nil {
                return inputTwo
            } else {
                inputTwo = inputTwo?.superview
            }
        }
        return nil
    }
    
 }
 
 {% endhighlight %}
 
Hopefully this adds some clarity to the relationships between views and how to work with them. Checkout the gist link to see the full implementation.
 
 Sources:
 
 * [http://smnh.me/images/hit-test-view-hierarchy.png](http://smnh.me/images/hit-test-view-hierarchy.png)
 * [Apple Docs: View and Window Architecture](https://developer.apple.com/library/content/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/WindowsandViews/WindowsandViews.html#//apple_ref/doc/uid/TP40009503-CH2-SW1)
