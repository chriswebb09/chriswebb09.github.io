---
title: "Finding The Common Superview"
layout: post
date: 2017-05-10 01:48
image: /assets/images/ios_logo_black.png
headerImage: false
tag:
- Swift
- UIView
- iOS
star: false
category: blog
author: chriswebb
description: Traversing the view tree
---


---

![View Hierachy](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/hit-test-view-hierarchy.png)

[Gist](https://gist.github.com/chriswebb09/1e4e5a1aa5f2e6fa3896c0a28975c5e7)


### Brief Introduction To The View Hierarchy In iOS

One of the most basic building blocks of any iOS app is the UIKit class UIView. UIViews or Views, define what happens within a given rectangular area of your screen. Almost all the elements in your application will be some form of view. Those elements that aren't, like UIImage, will be attached to a view type when displayed. If your application displays more than one view at a time, you will need to add these views to your root view. If you haven't created views programmatically, you can add a child view to a view calling the addSubview method from the parent view and specifying the child view you want to add like so:

{% highlight swift %}

yourViewName.addSubview(childView)

{% endhighlight %}

Views are layered on top of each other creating the view hierarchy.

### The View Hierarchy

##### When A View Hits Your Eye Like A Big Pizza Pie

![Pizza](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/error-sadPizza.jpg)

Think of your application's views as a pizza. The root is the dough layer, which the sauce layer sits on top of. Most pizzas have cheese and possibly other toppings. When you order a pizza with half pepperoni and half olive, both of these toppings sit on the same level, which is on top of the cheese. Views can also sit on the same level and have a common parent or 'superview.'

##### How The View Hierarchy Relates To Development

![View Hierarchy](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/ViewHierarchy0_alt.png)

How the views are layered and at which level defines our application's view hierarchy. When you build your application, it is something to try to stay aware of. At the most basic level, the view hierarchy encompasses the parent-child relationships between different views in your application. How you structure this will determine how your application looks and how you can interact with it. The view hierarchy is an important enough aspect of iOS development that Apple devotes a section of its View Programming Guide to the topic.

From Apple's View Programming Guide:

_Managing view hierarchies is a crucial part of developing your application’s user interface. The organization of your views influences both the visual appearance of your application and how your application responds to changes and events. For example, the parent-child relationships in the view hierarchy determine which objects might handle a specific touch event. Similarly, parent-child relationships define how each view responds to interface orientation changes._

##### Superviews

In the parent-child view relationshi a superview is the parent view. All views have a superview property which is an optional view. As the parent view, the superview is higher up in the view tree than it's children (or subviews.) Being higher on the view tree means it is closer to the root view.

{% highlight swift %}

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

{% highlight swift %}

Optional(<UIView: 0x7ffde0500250; frame = (0 0; 0 0); tag = 1; layer = <CALayer: 0x6080000273c0>>)

{% endhighlight %}

The property is optional because it could be the root view and have no superview.

##### Subviews

All views have an array property called subviews. This array contains views for which it is the parent view. This array is not optional, because views can add subviews, but there may or may not be any elements in the subviews array property.

{% highlight swift %}

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

{% highlight swift %}

[<UIView: 0x7fc1be800da0; frame = (0 0; 0 0); tag = 2; layer = <CALayer: 0x610000037080>>, <UIView: 0x7fc1be801050; frame = (0 0; 0 0); tag = 3; layer = <CALayer: 0x6100000370a0>>]

{% endhighlight %}

#### Apple-y Definition:

Apple describes superview, subviews in the following manner:

_When one view contains another, a parent-child relationship is created between the two views. The child view in the relationship is known as the subview and the parent view is known as the superview. The creation of this type of relationship has implications for both the visual appearance of your application and the application’s behavior._

### Getting Started

Sometimes it is not immediately obvious which view is the common superview for two different views. So, how would we go about finding the common superview? There are a few steps we need to go through first. Let's take this problem one view at a time. To begin with, let's create a class called ViewTraverser which will be responsible for functionality for finding our common superview (if it exists!)


### Creating A View-Tag Table

To start with let's create a private function **traverseSuperViews(view: UIView) -> [Int : UIView]**. This should take in a view parameter and return a table/dictionary with the parameter's superviews. For the sake of this exercise, I'm going to assume that each view has been given a unique integer as a tag property. We are going to use that tag as the key for each corresponding view in the table. Why should this method be private? This traverseSuperViews is just one piece of the puzzle. It will only be used by a method within the ViewTraverser class.

{% highlight swift %}

class ViewTraverser {
    private func traverseSuperViews(view: UIView) -> [Int : UIView] {
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

This should give us our *dictionary* with the *view tag key, view value*. We can now use this table to lookup of the tags from our other view's superviews. If that lookup returns a value that is not nil we know that we have found a common superview and we can return it.

### Checking For A Superview

Let's create a method called **checkForSuper(view: UIView?, views: [Int: UIView]) -> UIView?** This method should also be private and it should take in a view and dictionary with *Int : View* pairs as parameters and returns an *optional view*. Why is our return type optional? Because our views could be completely unrelated and therefore have no common superview.

{% highlight swift %}

class ViewTraverser {

  // Traverse superviews

    private func checkForSuper(view: UIView?, views: [Int: UIView]) -> UIView? {
        guard let view = view else { return nil }
        if views[view.tag] != nil {
            return view
        }
        return nil
    }
}

{% endhighlight %}

### Putting It All Together

Finally, let's combine our functionality into a method called **commonSuper(viewOne: UIView, viewTwo: UIView) -> UIView?**  which takes in two views as parameters and returns an optional view. This method will be the main interface for our ViewTraverser class and should not get set to private like the other methods.

{% highlight swift %}

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

You should now be able to check whether any two given superviews have a common superview.

### Making Sure It Works

Let's see if our *ViewTraverser* can find a common superview. To test this, create a *ViewController* class and add the following:

{% highlight swift %}

import UIKit

class ViewController: UIViewController {

    let viewOne = UIView()
    let viewTwo = UIView()
    let viewThree = UIView()
    let intermediaryView = UIView()
    let viewSuper = UIView()
    let traverser = ViewTraverser()

    override func viewDidLoad() {
        super.viewDidLoad()
        viewOne.tag = 22
        viewTwo.tag = 23
        viewSuper.tag = 10
        view.tag = 12
        intermediaryView.addSubview(viewOne)
        intermediaryView.tag = 8
        viewThree.tag = 33
        viewThree.addSubview(viewTwo)
        view.addSubview(viewThree)
        view.addSubview(intermediaryView)
        // logic to check our common superviews goes here
    }
}

 {% endhighlight %}

We're almost there, let's finish this up! There are two lines that you need to add at the bottom of your **viewDidLoad** to run the check for the common superview:

 {% highlight swift %}

import UIKit

class ViewController: UIViewController {
    // Properties

     override func viewDidLoad() {
        // setup
        let nameView = traverser.commonSuper(viewOne: viewOne, viewTwo: viewTwo)
        print(nameView?.tag)
     }   
}

{% endhighlight %}

If everything goes according to plan, the following should get printed to your terminal:

{% highlight swift %}

Optional(12)

{% endhighlight %}

The first common superview for *viewOne* and *viewTwo* should be the ViewController's view, which was given the tag *12*.

### Wrap Up

Hopefully, this adds some clarity to the relationships between views and how to work with them. Checkout the gist link to see the full implementation.

 Sources:

 * [http://smnh.me/images/hit-test-view-hierarchy.png](http://smnh.me/images/hit-test-view-hierarchy.png)
 * [Apple Docs: View and Window Architecture](https://developer.apple.com/library/content/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/WindowsandViews/WindowsandViews.html#//apple_ref/doc/uid/TP40009503-CH2-SW1)
