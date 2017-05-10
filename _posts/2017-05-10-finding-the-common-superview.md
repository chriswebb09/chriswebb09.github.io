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

When programming your iOS application, one thing you will need to be aware of is the view hierarchy. At it’s most basic level, the view hierarchy encompasses the parent-child relationships between different views in your application. How you structure this will determine how your application looks and you can interactive with it.

#### Apple-y Definition: 

_When one view contains another, a parent-child relationship is created between the two views. The child view in the relationship is known as the subview and the parent view is known as the superview. The creation of this type of relationship has implications for both the visual appearance of your application and the application’s behavior._

##### Superviews

A superview is a view that is higher on the view tree than it’s subviews. Another way to put this is that a superview is the parent view of a subview. Being higher on the view tree means it is closer to the rootview.

##### Subviews 

It logically goes, that subviews are the child views of a parent and are further down the view hierarchy than their parent. In fact you can check for a view's subviews by accessing the subviews property which will return an array of views that are subviews if there are any subviews.

### Getting Started

Sometimes it is not immediately obvious which view is the superview. So how would we go about finding the common superview for two views? To begin with, lets focus on the first input view. We can create an array of views to represent the view hierarchy by looping through the superview like so:

{% highlight swift linenos %}

class ViewTraverser {
    func traverseSuperViews(view: UIView) -> [UIView] {
        var views: [UIView] = []
        var inputView: UIView = view
        views.append(inputView)
        while inputView.superview != nil {
            inputView = inputView.superview!
            views.append(inputView)
        }
        return views
    }
}

{% endhighlight %}

You should now have the view hierarchy for one view in an array which we can now use for comparison. Lets create a method that takes in a view 
and compares it to an array of views. If the view is contained within the array, return true else return false. 

{% highlight swift linenos %}

class ViewTraverser {

  // Traverse superviews 
  
    func checkForSuper(view: UIView?, views: [UIView]) -> Bool {
        guard let view = view else { return false }
        if views.contains(view) {
            return true
        }
        return false
    }
}

{% endhighlight %}

Now we can combine the two into a method that takes in two views and returns an optional view. The view should be optional because there may not be a 
common superview. 

{% highlight swift linenos %}

class ViewTraverser {

  // Traverse superviews
  
  // Check for superview 
  
      func commonSuper(viewOne: UIView, viewTwo: UIView) -> UIView? {
        var inputTwo: UIView? = viewTwo
        let superViews: [UIView] = traverseSuperViews(view: viewOne)
        while inputTwo != nil {
            if checkForSuper(view: inputTwo, views: superViews) {
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
