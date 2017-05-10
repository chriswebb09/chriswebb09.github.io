---
layout: post
title: Finding The Common Superview 
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

![View Hierachy](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/hit-test-view-hierarchy.png)

### Brief Introduction To The View Hierarchy In iOS

When programming your iOS application, one thing you will need to be aware of is the view hierarchy. At it’s most basic level, the view hierarchy
encompasses the parent-child relationships between different views in your application. How you structure this will determine how your application
looks and you can interactive with it.

##### Superviews

A superview is a view that higher on the view tree than it’s subviews. Another way to put this is that a superview is the parent view of a subview.  Its position is higher on the view tree and closer to the root view.

##### Subviews 

It logically goes, that subviews are the child views of a parent and are further down the view hierarchy than their parent. In  fact you can check for a views subviews by accessing the subviews property which will return an array of views that are subviews, if there are any subviews.

### Getting Started

Sometimes it is not be immediately obvious which view is the superview. So how would we go about finding the common superview for two views? To begin
with lets focus on the first input view. We can create array of views to represent the view hierarchy by looping through the superview like so:

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
