---
layout: post
title: Executing On Background Thread And Updating To Main 
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

![Downloaded Image](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/doggo.png)

### Background

As I’ve written here before, network requests can throw a wildcard into the performance of your application. The nature of these problems can vary, from response time to the amount information a request returns. One way you can mitigate issues is by performing these requests on background threads and dispatching the UI updates that result from them to the main thread. 

### Dispatch Framework

To help manage threading, Apple provides developers with the Dispatch framework. 

_Dispatch comprises language features, runtime libraries, and system enhancements that provide systemic, comprehensive improvements to the support for concurrent code execution on multicore hardware in macOS, iOS, watchOS, and tvOS._

One easy way to use Dispatch is with the DispatchQueue class. Apple describes DispatchQueue as:

_DispatchQueue manages the execution of work items. Each work item submitted to a queue is processed on a pool of threads managed by the system._

In practical terms what this means is it allows developers to decide on which thread their code will execute.  Multithreading is a complicated topic, and for this post, it is not important to get into detail. 

### Let's get started! 

#### The main thread.

In iOS the main thread executes synchronously. Why is this important? Synchronous queue means that operations run one after the other in order. For development purposes what that means is that if something is running synchronously, it waits for each operation to finish before moving on to the next one.

#### UIApplication and Main 

One other thing I should mention, UIApplication executes on the main thread. So, if something on your main thread takes a long time to complete, it will hold up your entire application. That means, for the most part, you should only use the main thread for processes that you know will finish in a short period, like for instance UI updates. Running a long and complicated operation on the main thread will destroy your Application's responsiveness. 

#### Code

To start, let's create a protocol to define the behavior our Class should implement: 

{% highlight swift linenos %}

protocol ImageDownloadProtocol {
    var image: UIImage? { get set }
    func downloadImage(from url: URL, completion: @escaping (UIImage?) -> Void)
}

{% endhighlight %}

Any class that conforms to the ImageDownloadProtocol must have a image property and a func downloadImage(from url: URL, completion: @escaping (UIImage?) -> Void) method. One thing we could do to simplify our code is to add a default implementation for our method in a protocol extension. That will allow any class that conforms to that protocol to use a default download image implementation. 

Let's extend ImageDownloadProtocol to give a default implementation for the method:

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

Finally we can make our ViewController conform to the class ImageDowloadProtocol:

{% highlight swift linenos %}

class ViewControllerOne: UIViewController, ImageDownloadProtocol {
    
    var image: UIImage?
    let imageViewOne = UIImageView()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        imageViewOne.frame = UIScreen.main.bounds
        view.addSubview(imageViewOne)
        let url = URL(string: "http://cdn-www.dailypuppy.com/dog-images/colby-the-golden-retriever_77539_2016-09-16_w450.jpg")!
        downloadImage(from: url) { image in
            if Thread.isMainThread {
                print("thread is main")
            }
            self.imageViewOne.image = image
        }
    }
}

{% endhighlight %}

Since our completion is dispatched to the main the, the code within the closure is executed on the main thread. You can check to make sure of this with the following: Thread.isMainThread 

Sources: 

[raywenderlich.com - GCD in Swift 3](https://www.raywenderlich.com/148513/grand-central-dispatch-tutorial-swift-3-part-1)
[Apple Docs - Dispatch](https://developer.apple.com/reference/dispatch)
[Apple Docs - DispatchQueue](https://developer.apple.com/reference/dispatch/dispatchqueue)
