---
title: "Coordinators: Re-examined"
layout: post
date: 2018-01-02 01:48
image: /assets/images/coordinatorss.png  
headerImage: true
tag:
- Swift
- UIView
- iOS
- ARKit
- iOS Architecture
category: blog
author: chriswebb
description: "Coordinators: Re-examined"
---

#### Taming Unruly Application Architecture

{% highlight html %}
{% endhighlight %}

![](https://cdn-images-1.medium.com/max/980/1*rwLW_2iTuvsJibZ3f9Wu1g.png)<a href="https://will.townsend.io/img/coordinator-diagram.svg">Source</a>

#### Back To Coordinators

So it has been a few months since I wrote my previous post on coordinators. In the interim, a lot has happened! I published a new application called PodCatch (which I built using the coordinator concept — still work in progress):

[chriswebb09/podcatcher](https://github.com/chriswebb09/podcatcher)
{% highlight html %}
{% endhighlight %}
And I attended/volunteered at the [try! Swift Conference in New York&nbsp;City](https://medium.com/@jp_pancake/try-swift-nyc-2017-tryswiftnyc-ce3eab349914):

![](https://cdn-images-1.medium.com/max/166/1*PKi25dN31KSAUjlT8SM22g.jpeg)
{% highlight html %}
{% endhighlight %}
Me

Along the way I’ve had a chance to explore the concept in greater detail and expirement to find what features have worked for&nbsp;me.

#### Circling On&nbsp;Back

I feel like now is a good time to circle back and finish up my series on coordinators. Before I get too far, I want to own up to something first. I don’t think my first post was a great introduction to the topic. It lacks focus since I tried to introduce too many concepts into something that is much more narrow in scope. In addition to this post, there will most likely be a part three to this series, because this post isn’t complete in and of itself and I’m not happy with what I originally wrote. I know circling back now is a bit of a non-sequitur from the series I’ve been writing on CoreLocation as well as the example project I hope to have out (next week tentative). I’m writing this now because I want to get it down while the conversations I had on the topic at the try! Swift conference in NYC this past week are still fresh in my mind. **¯\_(**ツ**)\_/¯**

{% highlight html %}
{% endhighlight %}

### The Application Controller/ Coordinators

{% highlight html %}
{% endhighlight %}

So let’s do a bit of review from part one, if you’ve already read that post, this might seem a bit redundant. One of the problems with view controllers is that they can become massive and unwieldy as your application development progresses. This is because their purpose can be interpreted broadly. If most of your application works within views and a view controllers ‘controls’ views then it seems perfectly reasonable to place much of your application logic within it. Obviously there are serious drawbacks to this approach. If you’ve read [Soroush Khanlou’s blog posts on coordinators](http://khanlou.com/2015/10/coordinators-redux/) then this is something extra, if you haven’t I’m just briefly going to touch on some background information.

{% highlight html %}
{% endhighlight %}

> Unfortunately, a view controller-centric approach to app development isn’t scalable. The Coordinator is a great way to isolate your code into small, easily-replaced chunks, and another part of the solution to view controller malaise.> — [Khanlou: The Coordinator](http://khanlou.com/2015/01/the-coordinator/)

{% highlight html %}
{% endhighlight %}

The coordinator pattern is a enterprise software design pattern that has been adapted to the iOS&nbsp;world:

{% highlight html %}
{% endhighlight %}

> You can remove duplication by placing all the flow logic in an Application Controller. Input controllers then ask the Application Controller for the appropriate commands for execution against a model and the correct view to use depending on the application context.> — [Martin&nbsp;Fowler](https://martinfowler.com/eaaCatalog/applicationController.html)

![](https://cdn-images-1.medium.com/max/524/1*j_JHNi6hTI_nZi2G3Or1Ow.gif)<a href="https://martinfowler.com/eaaCatalog/applicationController.html">Source</a>

{% highlight html %}
{% endhighlight %}

I like the tl;dr they posted at the top, it’s a pretty succinct definition of what coordinators are trying to accomplish:

{% highlight html %}
{% endhighlight %}

_A centralized point for handling screen navigation and the flow of an application._

{% highlight html %}
{% endhighlight %}

For the purposes of this post I want place as much emphasis on the flow of the application as on navigation.

### Observations

{% highlight html %}
{% endhighlight %}

I spoke to a few developers at the try! Swift conference in NYC about their use of coordinators and everyone I spoke to had a different interpretation of their limits and responsibilities. Some of the developers I spoke to had defined functionality that is a requirement for anything being labeled a coordinator like a start method. Others used a coordinator protocol without any methods being required to bind the classes controlling their application architecture together.

{% highlight html %}
{% endhighlight %}

From what I gleaned from my conversation with Soroush, the range of interpretations is sort of by&nbsp;design.

> So what is a coordinator? A coordinator is an object that bosses one or more view controllers around. Taking all of the driving logic out of your view controllers, and moving that stuff one layer up is gonna make your life a lot more awesome. — [Khanlou](http://khanlou.com/2015/01/the-coordinator/)

Beyond the broad strokes of this definition, it’s really up to developers to decide how and when to use&nbsp;them.

### Some Issues I’ve Come&nbsp;Across

{% highlight html %}
{% endhighlight %}

#### Coordinator/Delegate Hybrids

{% highlight html %}
{% endhighlight %}

All that being said, there were a few things that I’ve noticed when building out applications using coordinators. Because coordinators don’t have strict limitations on their responsibility, you can easily get carried away with them. One of the biggest issues I noticed with my projects is they can become coordinators view/view controller delegate hybrids. This isn’t necessarily a bad thing, however taken to an extreme we can end up in the same place we were trying to avoid in the first place by using coordinators: Massive-Coordinator-Controllers. You shouldn’t have to choose between using traditional iOS delegation and coordinators. In fact, finding a place for both has helped provide a natural structure to the pattern when there are very few guidelines.

#### Services

{% highlight html %}
{% endhighlight %}

Services tend be a broad umbrella. In fact I probably use the category too broadly in many of my applications. If you think about it in relation to the real world, services take some kind of currency and and perform some action in return. In iOS a service can be as strict as a network client, or as broad as anything that works and transforms your models/UI. Figuring out how you want to layer your application and where each piece of functionality is relegated can provide a natural structural integrity to your architecture over the course of development. One thing I’ve taken away from the last few months is it can be helpful to decide how strict or broad you want to be with what classify as a service before you get&nbsp;started.

If I were rewriting the example project, I probably would not include a UI&nbsp;service.

{% highlight html %}
{% endhighlight %}

{% highlight swift %}

{% highlight html %}
{% endhighlight %}

import UIKit

protocol Service { }

protocol UIStyleService: Service {
    var backgroundColor: UIColor { get set }
}

struct RedUIStyleService: UIStyleService {
    var backgroundColor: UIColor = .red
}

struct BlueUIStyleService: UIStyleService {
    var backgroundColor: UIColor = .blue
}

struct StartUIStyleService: UIStyleService {
    var backgroundColor: UIColor = .green
}

struct ApplicationService {

    var styleService: UIStyleService

    init?(style: UIStyleService?) {
        guard let style = style else { return nil }
        styleService = style
    }

    mutating func change(style: UIStyleService) {
        styleService = style
    }
}

{% endhighlight %}

{% highlight html %}
{% endhighlight %}

### Coordinators

{% highlight html %}
{% endhighlight %}

I went through a few iterations for example code for this blog post. My first attempt looked something like&nbsp;this:

{% highlight swift %}
import UIKit

protocol CoordinatorDelegate: class { }

protocol Coordinator: class {
    func start()
}
{% endhighlight %}

This isn’t bad, but at the end of the project I found myself writing this out, just to ensure I used the start method in my main coordinator:

```
func start() {
    childCoordinators[0].start()
}
```

{% highlight html %}
{% endhighlight %}

For me this was a clue that I wasn’t being broad enough with the overall coordinator protocol as well as not being specific enough with the behaviors of specialized coordinator subtypes.

What I finally settled on was&nbsp;this:

{% highlight swift %}
import UIKit

protocol CoordinatorDelegate: class { }

protocol Coordinator: class { }
{% endhighlight %}

### Coordinator Subtypes

{% highlight html %}
{% endhighlight %}

#### Main Coordinator

{% highlight html %}
{% endhighlight %}

One of the interesting things I came across when building PodCatch was the need to delineate different coordinators for types of logical flow. Although I eventually removed it, in my first version of PodCatch I had a sign in/sign up screen for a FireBase backend which was logically siloed off from the Podcast player. The responsibilities were so different that it seemed logical to separate the two areas. The question then arises, if these areas are separated do we need to control the flow between between them? In my case I went with an unequivocal yes.

#### One Abstraction To Rule Them&nbsp;All

{% highlight html %}
{% endhighlight %}

Here is where I could see myself reading this article and saying: “Okay this guy is getting carried away.” You might be right! All I ask is that you hear me out on this. Remember this quote from a few paragraphs up?

Soroush: “_So what is a coordinator? A coordinator is an object that bosses one or more view controllers around. Taking all of the driving logic out of your view controllers, and moving that stuff one layer up is gonna make your life a lot more awesome.”_

{% highlight html %}
{% endhighlight %}

Here’s the thing: I don’t see a reason this should only be true for view controllers. What’s stopping us from expanding on that idea one step further by layering our coordinations to make it that much more awesome! If we have clear sections of our applications whose internal coordination flow is separated, doesn’t it stand to reason that we can abstract coordination one step&nbsp;further?

#### Coordinators For Our Coordinators

{% highlight html %}
{% endhighlight %}

 ¯\_(ツ)\_/¯

{% highlight html %}
{% endhighlight %}

Just bear with me for a bit longer. Let’s build the coordinator protocol for our top coordinator:

{% highlight swift %}

import Foundation
protocol AppCoordinator: Coordinator {
    weak var delegate: CoordinatorDelegate? { get set }
    var childCoordinators: [Coordinator] { get set }
}
{% endhighlight %}

After that we can create class MainCoordinator which conforms to our AppCoordinator protocol like&nbsp;below:

{% highlight swift %}

import Foundation

class MainCoordinator: AppCoordinator {

    var childCoordinators: [Coordinator] = []

    var delegate: CoordinatorDelegate?

    func addChildCoordinator(_ childCoordinator: Coordinator) {
        childCoordinators.append(childCoordinator)
    }

    func removeChildCoordinator(_ childCoordinator: Coordinator) {
        childCoordinators = childCoordinators.filter { $0 !== childCoordinator }
    }
}
{% endhighlight %}

{% highlight html %}
{% endhighlight %}

After that we use protocol conformance to handle the flow between the different areas of our application:

{% highlight html %}
{% endhighlight %}

{% highlight swift %}
import UIKit

class MainCoordinator: AppCoordinator {

    var delegate: ControllerCoordinatorDelegate?

    var childCoordinators: [ControllerCoordinator] = []

    var window: UIWindow

    init(window: UIWindow) {
        self.window = window
        transitionCoordinator(type: .start)
    }

    func addChildCoordinator(_ childCoordinator: ControllerCoordinator) {
        childCoordinator.delegate = self
        childCoordinators.append(childCoordinator)
    }

    func removeChildCoordinator(_ childCoordinator: Coordinator) {
        childCoordinators = childCoordinators.filter { $0 !== childCoordinator }
    }
}

extension MainCoordinator: ControllerCoordinatorDelegate {

    func transitionCoordinator(type: CoordinatorType) {
        childCoordinators.removeAll()
        switch type {
        case .app:
            let redVC = RedViewController(service: ApplicationService(style: RedUIStyleService())!)
            let testCoordinator = ColorControllerCoordinator(window: window)
            redVC.delegate = testCoordinator
            addChildCoordinator(testCoordinator)
            testCoordinator.type = .red
            testCoordinator.start()
        case .start:
            let startVC = StartViewController()
            let testCoordinator = StartControllerCoordinator(window: window)
            startVC.delegate = testCoordinator
            addChildCoordinator(testCoordinator)
            testCoordinator.type = .start
            testCoordinator.start()
        }
    }
}
{% endhighlight %}

The interesting thing about this design is it allows flexibility with the different areas of our application. Each section doesn’t necessarily need to have one overarching flow. Within a tabbar, we can have several different coordinator flows going for each&nbsp;tab.

{% highlight html %}
{% endhighlight %}

As an added bonus, the ability to swap out, add or remove flows allows the architecture to work with various conditions say like having a setup profile flow until a user sets up their profile and then swapping that out with a view profile flow. That can be extrapolated out even further for different preferences etc.

### Controller Coordinator

{% highlight html %}
{% endhighlight %}

#### Side Note On Lessons Learned While Building&nbsp;PodCatch

{% highlight html %}
{% endhighlight %}

One of things I would do differently if I was building PodCatch over from scratch is the way I went about creating the controller coordinator subtypes. In particular I regret creating a separate tab and navigation coordinator subtypes. It added additional complexity that, looking back, kept me from creating as organized architecture as I would have&nbsp;liked.

{% highlight html %}
{% endhighlight %}

**NavigationCoordinator:**

{% highlight swift %}
import UIKit

protocol NavigationCoordinator: Coordinator {
    var navigationController: UINavigationController { get set }
}
{% endhighlight %}

**TabCoordinator:**

{% highlight swift %}
import UIKit

class TabCoordinator: NavigationCoordinator {

    var type: CoordinatorType = .tabbar
    weak var delegate: CoordinatorDelegate?
    var dataSource: BaseMediaControllerDataSource!
    var navigationController: UINavigationController

    required init(navigationController: UINavigationController) {
        self.navigationController = navigationController
    }

    convenience init(navigationController: UINavigationController, controller: UIViewController) {
        self.init(navigationController: navigationController)
        navigationController.viewControllers = [controller]
    }

    func start() {
      // Unused Method Smell
    }

    fileprivate func showMediaController(mediaCollectionController: MediaCollectionViewController) {
        addChild(viewController: mediaCollectionController)
    }

    func addChild(viewController: UIViewController) {
        navigationController.viewControllers.append(viewController)
    }
}
{% endhighlight %}

**Unanswered Questions: Tab Bar Controller Coordinator?**

The biggest issue is figuring out how to integrate the coordinator flows into something like a tab bar controller. Should a tab bar itself have its own coordinator? Is that overkill? Possibly, I’m still wrestling with the best way to approach&nbsp;this.

{% highlight html %}
{% endhighlight %}

#### Back On&nbsp;Track

These are all interesting dilemas but following all the leads pulls this post in too many directions and ultimately muddies the waters. Let’s refocus on the example at hand. For this example project in my first go around I had something like this as a coordinator subtype.

{% highlight swift %}
import UIKit

protocol ControllerCoordinator: Coordinator {
    var window: UIWindow { get set }
    var rootController: UIViewController { get }
}
{% endhighlight %}

That was probably enough. I wanted to demonstrate the the separation between application areas so I added a controller coordinator delegate (say that ten times fast) to help transition between the start view and color views. I ultimately settled on was&nbsp;this:

{% highlight html %}
{% endhighlight %}

{% highlight swift %}
import UIKit

protocol ControllerCoordinatorDelegate: CoordinatorDelegate {
    func transitionCoordinator(type: CoordinatorType)
}

typealias RootController = UIViewController & Controller

protocol ControllerCoordinator: Coordinator {
    var window: UIWindow { get set }
    var rootController: RootController! { get }
    weak var delegate: ControllerCoordinatorDelegate? { get set }
}
{% endhighlight %}

For me, even this is starting to get a bit overly complex for foundational layer of the application architecture. It still works and if you can find a simpler way to implement the same idea go for it. For now I’m still okay with it, I’m hoping to find a better way the more I experiment.

#### ColorControllerCoordinator

{% highlight html %}
{% endhighlight %}

The first go around for implementing my ControllerCoordinator for this example was for the coordinator that handles my view controller subclass ColorViewController:


{% highlight swift %}
import UIKit

class ColorControllerCoordinator: ControllerCoordinator {

    var rootController: RootController!

    var services: ApplicationService!

    var window: UIWindow

    weak var delegate: ControllerCoordinatorDelegate?

    private var navigationController: UINavigationController {
        return UINavigationController(rootViewController: rootController)
    }

    var type: ControllerType {
        didSet {
            switch type {
            case .blue:
                services.styleService = BlueUIStyleService()
                let blueVC =  BlueViewController(service: services)
                blueVC.delegate = self
                show(colorController: blueVC)
            case .red:
                services.styleService = RedUIStyleService()
                let redVC = RedViewController(service: services)
                redVC.delegate = self
                show(colorController: redVC)
            case .start:
                delegate?.transitionCoordinator(type: .start)
            case .none:
                break
            }
        }
    }

    init(window: UIWindow) {
        self.window = window
        type = .none
        services = ApplicationService(style: BlueUIStyleService())
    }

    func start() {
        window.rootViewController = navigationController
        type = rootController.type
        rootController.viewDidLoad()
        window.makeKeyAndVisible()
        changeColor(view: rootController.view, for: rootController.type)
    }

    private func show(colorController: ColorController) {
        colorController.delegate = self
        rootController = colorController
    }
}

extension ColorControllerCoordinator: ColorControllerDelegate {

    func changeColor(view: UIView, for type: ControllerType) {
        self.type = type
        view.backgroundColor = services.styleService.backgroundColor
    }
}
{% endhighlight %}

{% highlight html %}
{% endhighlight %}

What immediately caught my eye with this implementation was the UI change happening in changeColor method. I think a better way to handle this is to inject styleService into the view and have the view handle up UI change/updates. So there’s definitely a refactor or two that can happen in here. Ultimately we’re working towards separating our concerns. For me, coordinators should not just be another place to throw code that isn’t the view controller.

### Key Takeaways

{% highlight html %}
{% endhighlight %}

There were a few things that stick out in my mind about coordinators that I picked up over the conference:

**Children should never tell their parents what to do** : One lesson that has stuck with me was something Brian at Raizlab mentioned: child controllers should never tell their parent controllers what to do. Meaning we shouldn’t be calling **_self.navigation_** controller from within a child view controller. Coordinators solve this for us, because in theory, with the logical abstraction of the application data flow out of our view controllers and into our coordinators we’re taking the decisions out of the parent/child view controllers hands. It’s one of those programming concepts that translates really well to human relationships (obviously with exceptions.)

#### Coordinators can be implemented in different ways:

{% highlight html %}
{% endhighlight %}

Beyond pushing the logical flow out of the view controller, there’s no “one way” or right way to go about implementing. Mao’s probably not the best person to quote in an iOS blog post — but it’s sort of a “let a hundred flowers blossom” type of concept. It’s quite interesting to see the various permutations that grow from its simplicity.

#### Sources:

{% highlight html %}
{% endhighlight %}

- [P of EAA: Application Controller](https://martinfowler.com/eaaCatalog/applicationController.html)
- [Khanlou The Coordinator](http://khanlou.com/2015/01/the-coordinator/)

If you’re interested, there are some really great blog posts out there with people demoing their take on the&nbsp;concept:

{% highlight html %}
{% endhighlight %}

- [Navigation coordinators](http://irace.me/navigation-coordinators)
- [Skye Freeman Swift - Coordinators](http://skyefreeman.io/programming/2016/02/23/playing_with_app_coordinators.html)
- [An iOS Coordinator Pattern](https://will.townsend.io/2016/an-ios-coordinator-pattern)
- [I am Simme](https://www.iamsim.me/the-coordinator-pattern/)

* * *
