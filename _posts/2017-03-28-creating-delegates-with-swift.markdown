---
title: "Creating Delegates in Swift"
layout: post
date: 2017-03-28 01:48
image: /assets/images/ios_logo_black.png
headerImage: true
tag:
- Swift
- Delegation
- iOS
category: blog
author: chriswebb
description: "Creating Delegates in Swift"
---


![Print commands](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/Screen%20Shot%202017-03-28%20at%204.07.26%20PM.png)

## Overview

As you might have noticed, Apple is pretty trigger happy when it comes to providing delegate in its libraries.
There is UITableViewDelegate, UITextFieldDelegate and of course, your AppDelegate.  Delegation is one of the key software design
patterns in Object-Oriented programming. This pattern allows one class to hand off some functionality to another.

## The Apple-y explanation

### Apple definition:

_Delegation is a simple and powerful pattern in which one object in a program acts on behalf of, or in coordination with, another object.
The delegating object keeps a reference to the other object—the delegate—and at the appropriate time sends a message to it. The message
informs the delegate of an event that the delegating object is about to handle or has just handled. The delegate may respond to the message
by updating the appearance or state of itself or other objects in the application, and in some cases it can return a value that affects
how an impending event is handled. The main value of delegation is that it allows you to easily customize the behavior of several objects
in one central object._

## Diving In

Let’s imagine for a second you want to detect a button tap in your view and do something accordingly in your ViewController.

{% highlight swift %}
class MainView: UIView {

    @IBOutlet weak var myTextfield: UITextField!
    @IBOutlet weak var myButton: UIButton!

}
 {% endhighlight %}


{% highlight swift %}
class ViewController: UIViewController, MainViewDelegate {

    @IBOutlet var mainView: MainView!

    override func viewDidLoad() {
        super.viewDidLoad()
        mainView.myButton.addTarget(self, action: #selector(buttonTap), for: .touchUpInside)
    }

    func buttonTap() {
       print("Button was tapped")
    }
}

{% endhighlight %}

## Analysis

Does it work? Sure, but there is another way to do this where we don’t have to attach our button and ViewController directly.
We can create a delegate method to have MainView be the intermediary between the two. Doing this allows better for encapsulation of data and behaviors within respective classes.

Let's first create our protocol:

{% highlight swift %}
protocol MainViewDelegate: class {
    func searchButtonTappedWithTerm(with searchTerm: String)
}
 {% endhighlight %}


This is relatively straight forward. We have a class protocol called MainViewDelegate which specifies the method:

func searchButtonTappedWithTerm(with searchTerm: String)

To be able to use this delegate in code we need to add it our MainView as a weak optional property. Why weak? Because we don't want to have our ViewController and delegate locked in a strong reference cycle that stays in memory without being deallocated. That's a great way to cause a memory leak.

{% highlight swift %}
class MainView: UIView {

    @IBOutlet private weak var myTextfield: UITextField!
    @IBOutlet private weak var myButton: UIButton!

    weak var delegate: MainViewDelegate?

    override func layoutSubviews() {
        super.layoutSubviews()
        myButton.addTarget(self, action: #selector(buttonTap), for: .touchUpInside)
    }

    func buttonTap() {
        guard let searchText = myTextfield.text else { return }
        delegate?.searchButtonTappedWithTerm(with: searchText)
    }

}
 {% endhighlight %}

 To access this functionality we need to make our ViewController conform to the MainViewDelegate protocol like so:

 {% highlight swift %}
 class ViewController: UIViewController, MainViewDelegate {

      // View controller stuff

      func searchButtonTappedWithTerm(with searchTerm: String) {
        print("The text in UITextField is: \(searchTerm)")
    }
 }
 {% endhighlight %}

 We can now do something with the textfield text in our ViewController without having to access it directly.


## Recap

 The final ViewController code for this example looks something like:

{% highlight swift %}
class ViewController: UIViewController, MainViewDelegate {

    @IBOutlet var mainView: MainView!

    override func viewDidLoad() {
        super.viewDidLoad()
        mainView.delegate = self
    }

    func searchButtonTappedWithTerm(with searchTerm: String) {
        print("The text in UITextField is: \(searchTerm)")
    }
}
{% endhighlight %}

![Example code](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/Screen%20Shot%202017-03-28%20at%203.31.43%20PM.png)

The great thing about delegation is that there are a wealth of ways to implement them beyond this example. Figuring out where and when to use them is the key.

### Sources

[Apple documentation for Delegation](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html)
