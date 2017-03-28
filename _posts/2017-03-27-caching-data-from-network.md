---
layout: post
title: Caching images in Swift
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

![Loading...](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/loading%20screen.png)


### Brief Overview

[Example Gist](https://gist.github.com/chriswebb09/ef9e780872174af102d82fda23e980ae#file-mtapiclient-swift)

Networking requests in your application can be detrimental to your user experience. This applies in particular when downloading images. 
You have to take into account that not all users will have blazing fast internet. Some of these users can have their internet throttled
which will slow down your download rates to a snail's pace. You also don’t want to harm your users by using up their allotted data 
for the month. So what to do? One of the more easiest ways to manage networking data use is to cache data so that you don’t re-download 
information, particularly resource heavy data like images. Here is where NSCache comes in. 

### What is NSCache? 

__Apple defines NSCache like this__: 

_An NSCache object is a mutable collection that stores key-value pairs, similar to an NSDictionary object. The NSCache class provides a
programmatic interface to adding and removing objects and setting eviction policies based on the total cost and number of objects in the 
cache._

This is a unecessarily wordy explanation for a data structure, similar to a dictionary, that temporarily stores information until it needs
more space or is no longer in use. This is unlike CoreData or user UserDefaults in that there is no permanence to it. 

__As wikipedia helpfully puts it__:

_In computing, a cache is a hardware or software component that stores data so future requests for that data can be served faster; the data stored in a cache might be the result of an earlier computation, or the duplicate of data stored elsewhere._

### Side Note 

I think it's important to mention here, in the example that I am using, the URLSession is ephemeral. Ephemeral means to be fleeting, or short lived. What that means for a URLSession is that everything is stored to RAM instead of the disk and purged automatically when the session is over. 

__Apple-y definition__:
_Ephemeral sessions do not store any data to disk; all caches, credential stores, and so on are kept in RAM and tied to the session. Thus, when your app invalidates the session, they are purged automatically

### Let's get started! 
To start off, let's define our image cache like so:

{% highlight swift linenos %}
let imageCache = NSCache<NSString, UIImage>()
{% endhighlight %}

As you can see, we are caching an image which can access with an NSString. A good key to store your image by is the URL from where it was 
downloaded. That way we can do a check before we start our network request. If it exists, we can then skip the whole networking business. 


### Defining our method
Define a function like the following: 

{% highlight swift linenos %}
func downloadImage(url: URL, completion: @escaping (UIImage?) -> Void) 
{% endhighlight %}

This function takes in an URL and returns a UIImage in the completion. We need to get the string format for our URL which we can do using
url.absoluteString and then casting it type NSString.  Let’s check to see whether our image exists in the cache with the following bit of
code.

{% highlight swift linenos %}
  if let cachedImage = imageCache.object(forKey: url.absoluteString as NSString) {
            completion(cachedImage)
        }
 {% endhighlight %}

If the image exists, we immediately pass it to our completion and exit the function. 


### Wrap up 

Example:

{% highlight swift linenos %}

static func downloadImage(url: URL, completion: @escaping (UIImage?) -> Void) {
        if let cachedImage = imageCache.object(forKey: url.absoluteString as NSString) {
            completion(cachedImage)
        }
        MTAPIClient.downloadData(url: url) { data, response, error in
            if error != nil {
                print(error?.localizedDescription ?? "Unable to get specific error")
                completion(nil)
            }
            if let imageData = data {
                DispatchQueue.main.async {
                    if let downloadedImage = UIImage(data: imageData) {
                        imageCache.setObject(downloadedImage, forKey: url.absoluteString as NSString)
                        completion(UIImage(data: imageData))
                    }
                }
            }
        }
    }

{% endhighlight %}

I posted a link for a gist with an example of how I used it in an api client. 

### Sources 

* [Apple documentation for NSCache](https://developer.apple.com/reference/foundation/nscache)
* [Wikipedia article on caching](https://en.wikipedia.org/wiki/Cache_(computing)#Web_cache)
* [Apple documentation for URLSession](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/URLLoadingSystem/Articles/UsingNSURLSession.html)
