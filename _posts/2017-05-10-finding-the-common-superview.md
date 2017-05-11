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

A superview is a view that is higher on the view tree than it’s subviews. Another way to put this is that a superview is the parent view of a subview. Being higher on the view tree means it is closer to the root view.

{% highlight swift linenos %}

let viewOne = UIView()
let viewTwo = UIView()
let viewThree = UIView()

viewOne.tag = 1
viewTwo.tag = 2
viewThree.tag = 3

viewOne.addSubview(viewTwo)
viewOne.addSubview(viewThree)

print(viewTwo.superview)

{% endhighlight %}

This should print out something that looks like: 

{% highlight swift linenos %}

Optional(<UIView: 0x7ffde0500250; frame = (0 0; 0 0); tag = 1; layer = <CALayer: 0x6080000273c0>>)

{% endhighlight %}

The property is optional because it could be the root view and have no superview. 

##### Subviews 

All views have an array property called subviews. This array contains views for which it is the parent view. 

{% highlight swift linenos %}

let viewOne = UIView()
let viewTwo = UIView()
let viewThree = UIView()

viewOne.tag = 1
viewTwo.tag = 2
viewThree.tag = 3

viewOne.addSubview(viewTwo)
viewOne.addSubview(viewThree)

print(viewOne.subviews)

{% endhighlight %}

This should print out something that looks similar to:

{% highlight swift linenos %}

[<UIView: 0x7fc1be800da0; frame = (0 0; 0 0); tag = 2; layer = <CALayer: 0x610000037080>>, <UIView: 0x7fc1be801050; frame = (0 0; 0 0); tag = 3; layer = <CALayer: 0x6100000370a0>>]

{% endhighlight %}

### Getting Started

Sometimes it is not immediately obvious which view is the common superview for two different views. So, how would we go about finding the common superview? Let's take this problem one view at a time. To begin with let's create a table to store the superviews we find. For the sake of this exercise I'm going to assume that each view has been given a unique integer as a tag property. We are going to use that tag as the key for each corresponding view in the table.

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

This should give us our dictionary with a tag key, view value. We can now use this table to lookup of the tags from our other view's superviews. If that lookup returns a value that is not nil we know that we have found a common superview and we can return it. Let's create a method called checkForSuper(view: UIView?, views: [Int: UIView]) -> UIView? This method takes in a view and dictionary with Int, View pairs and returns an optional view. Why is our return type optional? Because our views could be completely unrelated and therefore have no common superview. 

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

Finally, let's combine our functionality into a method called commonSuper(viewOne: UIView, viewTwo: UIView) -> UIView?  which takes in two views and returns an optional view. 

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
