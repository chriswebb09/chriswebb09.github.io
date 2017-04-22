---
layout: post
title: Testing Asynchronous Code In Swift
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

![XC Test](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/Test2.png)
[Gist](https://gist.github.com/chriswebb09/526c511b7faaa6c71f0bc32b8f894aa8)

### Brief Overview

Testing in iOS development is becoming more and more common. While not as prominent a feature as in languages like Ruby, it's adoption is increasingly widespread. As iOS development has become more and more professionalized, with business models revolving around it, it becomes less and less acceptable to have applications without tests. As your application grows over time, one of the more tasking aspects is to ensure that your new code plays well with what has already been written. Writing tests can help maintain performance as the codebase grows. If you're interested in learning more about the larger subject of what to test for in iOS, there's a great book on the subject by Orta Therox called [Pragmatic Testing](https://github.com/orta/pragmatic-testing). He is head of mobile at Art.sy in New York and has good deal of wisdom on the topic. 

There are many frameworks for testing in iOS. One of the most commonly used third party libraries for unit tests is [Quick](https://github.com/Quick/Quick). LinkedIn recently released a coding framework called [Blue Pill](https://github.com/linkedin/bluepill) For this post, I will stick with the default XCTest that Apple provides. In Apple’s XCTest framework, most test function will execute synchronously. You can run into problems when you test asynchronous code in the same way you would synchronous code. Code testing relies on the fundamental principle of running code and then checking to see whether it computes to predefined parameters. 

_Math.swift_

{% highlight swift linenos %}

class Math {
    func add(_ intOne: Int, _ intTwo: Int) -> Int {
        return intOne + intTwo
    }
}

{% endhighlight %}

_ExampleProjectTests.swift_

{% highlight swift linenos %}

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

### Some Notes On Asynchronous Testing 

__The Apple-y explanation__:

_Tests execute synchronously because each test is invoked independently one after another._

But, as Apple helpfully points out:

_But more and more code executes asynchronously._

There's a simple reason behind this phenomenon:
More and more, code relies on data from network requests to function. The inherent unpredictability of networking, the fact that there is 
no guarantee of when your data will arrive, means these request need to be handled asynchronously. 

### Getting Started 

Some good things to test in your application are complex sets of logic and logic the deals with data that is subject to change. Consistent aspects on your code, like a model is not necessarily the first priority. For this example I'm going to test the intermediary between the networking functionality and what is displayed on the screen.

## Living Up To Expectations

To begin with, let's create our expectation. An Expectation is a predefined outcome that you want your asynchronous code to perform. 
To do this, we'll need to create an instance of the XCTestExpectation class. When we create our expectation, we give it a string which 
describes the what we are expecting. While writing out a good description may not be necessary to run your test, the test log might be 
meaningless without it.

{% highlight swift linenos %}

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

The class that is the intermediary in my application is the iTrackDataStore. It takes in search paramters from the ViewController, sends them to the APIClient, and then takes the APIClient response and constructs an array of iTrack data objects. These objects are then passed back to the ViewController. Like with most code that relies on the network, iTrackDataStore executes asynchronously.  

{% highlight swift linenos %}

func testDataStore() {

// Setup test 

  dataStore.searchForTracks { tracks, error in
            XCTAssert(tracks?.count == 49)
            expect.fulfill()
   }
}

{% endhighlight %}

As you can see from the code above, XCTestExpectation doesn't only provide a description of what you are expecting, it also specifies when the expectation has been fufilled using the aptly names .fulfill() method. But what happens if something goes wrong?

{% highlight swift linenos %}
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

In the code above we call waitForExpections and give it a timeframe within which the code that you are testing should return the expected answer. If this doesn't happen, it asserts XCFail and logs and the error. 

#### Sources 

* [Apple Docs](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/04-writing_tests.html)

* [Mokacoding](http://www.mokacoding.com/blog/testing-callbacks-in-swift-with-xctest/)

* [Road Fire Software](http://roadfiresoftware.com/2016/08/testing-asynchronous-code-in-swift-with-xctest-expectations/)
