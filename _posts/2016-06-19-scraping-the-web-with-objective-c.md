---
layout: post
title: Scraping The Web Objective-C 
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

Using Objective C to get and format web data is not an overly complicated process, but it isn’t something that can be, (*or should be) done straight forward within a single method.

### Getting Started 
When tackling this process we need to consider what is required to get the data, then to format it in a way which will be meaningful or useful. Luckily, there are two classes that Apple provides in the Foundation Library that will simplify this process immensely. These are NSURL and NSRegularExpression classes. We will be using NSURL to pass in a web url and return the raw web data from the request. NSRegularExpression takes in a provided pattern which can then be used to find matching content within whatever data set you provide.

### Regex 
You may have heard of regular expressions or regexes before. Regexes are a way of specifying a pattern within data that is provided. It's why for example, a site will know whether or not a email address is in a valid format or whether a password is strong enough. In general it’s not necessary to learn regexes too in depth. Most patterns can be found easily through Googling. In this example we are going to be specifying a general url pattern which looks like: 

{% highlight objc linenos %}
@"http?://([-\\w\\.]+)+(:\\d+)?(/([\\w/_\\.]*(\\?\\S+)?)?)?";
{% endhighlight %}

We want our program to grab the raw text response from our web request so that it can then hand that off to the regular expression finder which will then go through all that data and find anything that matches

{% highlight html linenos %}
http://www.<whateveritfinds>.com 
{% endhighlight %}

 Every time it finds a match it should add that match to a data structure which can be returned at the end of the function.

### Raw Web Data
 First things first. Let's get our data from the web by creating a method that takes an NSString parameter, passedURL, and returns an NSString of the raw web content.


{% highlight objc linenos %}

 - (NSString*)getURLContentFromURL:(NSString *)passedURL {
    
    NSError* error = nil;
    NSURL *url = [NSURL URLWithString:passedURL];
    //Set url
    NSString *htmlData = [NSString stringWithContentsOfURL:url encoding:NSASCIIStringEncoding error:&error];
    //Get content from web using url variable are target.
    if (htmlData) {
        //If the response from the web request was htmlData
        NSLog(@"%@", htmlData);
    } else {
        //If response wasn't htmlData return error.
        NSLog(@"%@", error);
    }
    return htmlData;
}
{% endhighlight %}

If you use NSLog(@"%@", htmlData) you should see the overwhelming chaos that an unfiltered web request can return. In the next post, part 2, we'll go over what you should with that data you just scraped. 
