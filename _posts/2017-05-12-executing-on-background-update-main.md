---
layout: post
title: Executing-In-Background-Update-On-Main
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

# Introduction
I’ve written before about iOS networking and how network requests can throw a wildcard into application performance. The nature of the problem can vary, from response time to the amount information a request returns. One way you can mitigate issues is by performing these requests on background threads and dispatching the UI updates that result from them to the main thread.

### Dispatch Framework

To help manage threading, Apple provides developers with the Dispatch framework. Dispatch comprises language features, runtime libraries, and system enhancements that provide systemic, comprehensive improvements to the support for concurrent code execution on multicore hardware in macOS, iOS, watchOS, and tvOS. One easy way to use Dispatch is with the DispatchQueue class. 
Apple describes DispatchQueue as:

_DispatchQueue manages the execution of work items. Each work item submitted to a queue is processed on a pool of threads managed by the system._

In practical terms what this means is it allows developers to decide on which thread their code will execute. Multithreading is a complicated topic, and for this post, it is not important to get into detail.

# Let’s get started!

### The main thread

In iOS the main thread executes synchronously. Why is this important? Synchronous queue means that operations run one after the other in order. For development purposes what that means is that if something is running synchronously, it waits for each operation to finish before moving on to the next one.

### UIApplication and the Main Thread
One other thing I should mention, UIApplication is setup on the main thread. So, if something on your main thread takes a long time to complete, it could hold up your entire application. 

_An app’s main run loop processes all user-related events. The UIApplication object sets up the main run loop at launch time and uses it to process events and handle updates to view-based interfaces._

As the name suggests, the main run loop executes on the app’s main thread. For the most part, you should only use the main thread for processes that you know will finish in a short period, like UI updates. Running a long and complicated operation on the main thread will destroy your Application’s responsiveness.

# Our Code
To start, let’s create a ImageDownloadProtocol to define the download behavior that our class should implement:

{% highlight swift linenos %}

protocol ImageDownloadProtocol {
    func downloadImage(from url: URL, completion: @escaping (UIImage?) -> Void)
}

{% endhighlight %}

Any class that conforms to the ImageDownloadProtocol must have an implementation of the downloadImage(from url: URL, completion: @escaping (UIImage?) -> Void) method.

### Extending Our Protocol With Default Implementation
One thing we could do to simplify our code is to add a default implementation for our method in a protocol extension. That will allow any class that conforms to that protocol to use a default download image implementation.
Let’s extend ImageDownloadProtocol to give a default implementation for the method:


{% highlight swift linenos %}
extension ImageDownloadProtocol {
    func downloadImage(from url: URL, completion: @escaping (UIImage?) -> Void) {
        let session = URLSession(configuration: .default)
        DispatchQueue.global(qos: .background).async {
            print("In background")
            session.dataTask(with: URLRequest(url: url)) { data, response, error in
                if error != nil {
                    print(error?.localizedDescription ?? "Unknown error")
                }
                if let data = data, let image = UIImage(data: data) {
                    print("Downloaded image")
                    DispatchQueue.main.async {
                        print("dispatched to main")
                        completion(image)
                    }
                }
                }.resume()
        }
    }
}
{% endhighlight %}

### ViewController Conform To Protocol
Finally we can make our ViewController conform to our ImageDowloadProtocol:

