---
title: "Testing Asynchronous Code In Swift"
layout: post
date: 2017-04-22 01:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Swift
- Testing
- iOS
category: blog
author: chriswebb
description: "Testing Asynchronous Code In Swift"
---


![XC Test](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/Test2.png)
[Gist](https://gist.github.com/chriswebb09/526c511b7faaa6c71f0bc32b8f894aa8)

### Brief Introduction Testing on iOS

Testing is fast approaching standard operating procedure for iOS development. While it is not as prominent as in Ruby, its adoption is increasingly widespread. This means it's important to grasp the basic concepts. As iOS development continues to grow into a professionalized field, with business models revolving around it, it becomes less acceptable to have applications written without tests.

#### Why?

As your application grows over time, one of the more tasking aspects for developers is to ensure that your new code plays well with the code that is there. Writing tests can help maintain performance as the codebase grows.

#### Test Coverage

It might seem conterintuitive, but one of the hardest aspects of testing is deciding what you should test. There are blog posts devoted to this question, there are books devoted to it! While you want your code to be well tested, like everything in life, it takes time. If you're developing on a small team or by yourself, time is of the essence.

_In computer science, code coverage is a measure used to describe the degree to which the source code of a program is executed when a particular test suite runs._
__Wikipeida__

#### Open Source iOS Testing Book!

If you're interested in learning more about the larger subject of what to test for in iOS, there's a great open source book on the subject by Orta Therox called [Pragmatic Testing](https://github.com/orta/pragmatic-testing). He is head of mobile at Art.sy in New York and has good deal of wisdom on the topic.

### Tools for Testing
---
There are many frameworks for testing in iOS. One of the most commonly used third party libraries for unit tests is [Quick](https://github.com/Quick/Quick). LinkedIn recently released a coding framework called [Blue Pill](https://github.com/linkedin/bluepill) For this post, I will stick with the default XCTest that Apple provides.

#### Synchronous vs. Asynchronous

Let's define some terminology first. Asynchronous code is any code in your application that the executes out of order. Why would your application run out of order? The reasons are numerous, but I will give an explanation that hopefully clarifies this for you. Say you want to grab some JSON for an application. For whatever reason, the network is slow, the server is overloaded, the request takes longer than you were expecting. If that network request is executed synchronous, the rest of the application needs to wait for it to finish to move on. Particularly with a platform like iOS, where user experience is central to success, that is unacceptable. To provide a responsive experience, we can send our request and let our application do other things while we wait for the response. Networking is just one reason. You could also asynchronously execute code that is computationally taxing on your CPU.

There are a variety of ways to run tests while taking into account asynchronous code execution in your application. Each of these methods has their benefits and downsides. One common way to do this is by Mocking. Mocking is when you simulate some code execution. A lot of use cases for mocking revolves around network requests. There are a few good reasons you might want to mock a web request.  One reason you might use mocking is so you can ensure your tests are consistent. Like any good science experiment, you want to test against a control group. This ensures that you test against the exact outcome each time your test runs. One of the upsides of the Mocking also contains the major pitfall: mocking simulates behavior, but it is necessarily an accurate reflection of how it is performing the in the real world.

#### XCTest

In Apple’s XCTest framework, most test functions will execute synchronously. You can run into problems when you test asynchronous code in the same way you would synchronous code. Code testing relies on the fundamental principle of running code and then checking to see whether it computes to predefined parameters.

_Math.swift_

{% highlight swift %}

class Math {
    func add(_ intOne: Int, _ intTwo: Int) -> Int {
        return intOne + intTwo
    }
}

{% endhighlight %}

_ExampleProjectTests.swift_

{% highlight swift %}

import XCTest
@testable import ExampleProject

class ExampleProjectTests: XCTestCase {

    override func setUp() {
        super.setUp()
    }

    override func tearDown() {
        super.tearDown()
    }

    func testAdd() {
        let math = Math()
        XCTAssert(math.add(1, 1) == 2)
    }
}

{% endhighlight %}

#### Some Notes On Asynchronous Testing

__The Apple-y explanation__:

_Tests execute synchronously because each test is invoked independently one after another._

As Apple helpfully points out:

_But more and more code executes asynchronously._

There's a simple reason behind this phenomenon:

More and more, code relies on data from network requests to function. The inherent unpredictability of networking, the fact that there is
no guarantee of when your data will arrive, means these request need to be handled asynchronously.

### Getting Started

---

Some good things to test in your application complex blocks of logic. Consistent aspects on your code, like your model classes are not necessarily first priority. For this example I'm going to test the intermediary between the networking functionality and what is displayed on the screen.

#### Living Up To Expectations

To begin with, let's create our expectation. An Expectation is a predefined outcome that you want your asynchronous code to perform.
To do this, we'll need to create an instance of the [XCTestExpectation class](https://developer.apple.com/reference/xctest/xctestexpectation).

#### Appley Words:
_An expected outcome in an asynchronous test._

Apple can be hit or miss when it come to explaining stuff concisely and clearly, but in this case they're dead on. Couldn't have said it any better.

#### Creating Expectations

When we create our expectation, we give it a string which  describes the what we are expecting. While writing out a good description may not be necessary to run your test, the test log might be meaningless without it.

{% highlight swift %}

import XCTest
import AVFoundation
@testable import Musicly

class MusiclyTests: XCTestCase {

    // Boiler plate

    func testDataStore() {
        let dataSource = iTrackDataStore(searchTerm: "new")
        let expect = expectation(description: "Data store calls APIClient to access server data and returns iTrack data array.")

        // Rest of test

    }
}

{% endhighlight %}

#### Fulfilling Expectations

The class that is the intermediary in my application is the iTrackDataStore. It takes in search paramters from the ViewController, sends them to the APIClient, and then takes the APIClient response and constructs an array of iTrack data objects. These objects are then passed back to the ViewController. Like with most code that relies on the network, iTrackDataStore executes asynchronously.  

{% highlight swift %}

func testDataStore() {

// Setup test

  dataStore.searchForTracks { tracks, error in
            XCTAssert(tracks?.count == 49)
            expect.fulfill()
   }
}

{% endhighlight %}

As you can see from the code above, XCTestExpectation doesn't only provide a description of what you are expecting, it also specifies when the expectation has been fufilled using the aptly names .fulfill() method. But what happens if something goes wrong?

{% highlight swift %}
 func testDataStore() {

      // Setup
      // Run test

        waitForExpectations(timeout: 4) { error in
            if let error = error {
                XCTFail("waitForExpectationsWithTimeout error: \(error)")
            }
        }
    }
{% endhighlight %}

### Wrap Up
---
In the code above we call waitForExpections and give it a timeframe within which the code that you are testing should return the expected answer. If this doesn't happen, it asserts XCFail and logs and the error.

#### Sources

* [Apple Docs](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/04-writing_tests.html)

* [Mokacoding](http://www.mokacoding.com/blog/testing-callbacks-in-swift-with-xctest/)

* [Road Fire Software](http://roadfiresoftware.com/2016/08/testing-asynchronous-code-in-swift-with-xctest-expectations/)
