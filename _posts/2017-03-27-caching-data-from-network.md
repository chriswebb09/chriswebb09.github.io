---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

### Brief Overview
Networking requests in your application can be detrimental to your user experience. This applies in particular when downloading images. 
You have to take into account that not all users will have blazing fast internet. Some of these users can have their internet throttled
which will slow down your download rates to a snail's pace. You also don’t want to harm your users by using up their allotted data 
for the month. So what to do? One of the more easiest ways to manage networking data use is to cache data so that you don’t re-download 
information, particularly resource heavy data like images. Here is where NSCache comes in. 

### What is NSCache? 

Apple defines NSCache like this: 

An NSCache object is a mutable collection that stores key-value pairs, similar to an NSDictionary object. The NSCache class provides a
programmatic interface to adding and removing objects and setting eviction policies based on the total cost and number of objects in the 
cache.

This is a unecessarily wordy explanation for a data structure, similar to a dictionary, that temporarily stores information until it needs
more space or is no longer in use. This is unlike CoreData or user UserDefaults in that there is no permanence to it. 

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
        print(url)
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
    
    static func downloadData(url: URL, completion: @escaping (_ data: Data?, _  response: URLResponse?, _ error: Error?) -> Void) {
        let session = URLSession(configuration: .ephemeral)
        let urlRequest = URLRequest(url: url)
        session.dataTask(with: urlRequest) { data, response, error in
            print("Data: \(data)")
            print("Response: \(response)")
            print("Error: \(error?.localizedDescription)")
            print("URL: \(url.absoluteString)")
            completion(data, response, error)
            }.resume()
    }

{% endhighlight %}


### Sources 

* [Apple documentation for NSCache](https://developer.apple.com/reference/foundation/nscache)

