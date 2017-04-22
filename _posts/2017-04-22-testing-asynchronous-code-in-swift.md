---
layout: post
title: Testing Asynchronous Code In Swift
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

![Loading...](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/Test2.png)
[Gist](https://gist.github.com/chriswebb09/526c511b7faaa6c71f0bc32b8f894aa8)

### Brief Overview

In Apple’s XCTest framework, most test function will execute synchronously. You can run into problems when you test asynchronous code 
in the same way you would synchronous code. Code testing relies on the fundamental principle of running code and then checking to see 
whether it computes to predefined parameters. 

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

### Getting Started 

__The Apple-y explanation__:

_Tests execute synchronously because each test is invoked independently one after another._

But, as Apple helpfully points out:

_But more and more code executes asynchronously._

There's a simple reason behind this phenomenon:

More and more, code relies on data from network requests to function. The inherent unpredictability of networking, the fact that there is 
no guarantee of when your data will arrive, means these request need to be handled asynchronously. 

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

In the example about I'm test my iTrackDataStore, which acts as an intermediary between the ViewController and the APIClient. It executes asynchronously and returns an array of iTrack objects. 

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
