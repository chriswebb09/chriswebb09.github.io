---
layout: post
title: Scraping The Web With Objective-C 
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

Using Objective-C to get and format web data is not an overly complicated process, but it isn’t something that should be done straight forward within a single method. We will be using NSURL class to pass in a web url and return raw webpage and NSRegularExpression to make sense of that data. Part one will be primarily confined to the web request aspect of our task. In part two we will go over using NSRegularExpression to filter through the noise that the raw request returns so that you can find something useful.


### Brief Overview
When tackling this process let's first consider the steps involved in getting the final result. In this order (roughly) we need to do the following:

-specify the url to scrape data from 
-make our web request 
-store the return in a data structure to be used later


### Extra Info
When you send request a URL on the web it looks roughly something like this: 

{% highlight http linenos %}
GET /about.html HTTP/1.1
Host: www.objc.io
Accept-Encoding: gzip, deflate
Connection: keep-alive
If-None-Match: "a54907f38b306fe3ae4f32c003ddd507"
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
If-Modified-Since: Mon, 10 Feb 2014 18:08:48 GMT
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_2) AppleWebKit/537.74.9 (KHTML, like Gecko) Version/7.0.2 Safari/537.74.9
Referer: http://www.objc.io/
DNT: 1
Accept-Language: en-us
{% endhighlight %}

### Raw Web Data
 To get started, let's get our data from the web by creating a method that takes an NSString parameter, passedURL, which returns an NSString of the raw web content.


{% highlight objc linenos %}

 - (NSString*)getURLContentFromURL:(NSString *)passedURL {
    
    NSError* error = nil;
    //Set url
    NSURL *url = [NSURL URLWithString:passedURL];
    NSString *htmlData = [NSString stringWithContentsOfURL:url encoding:NSASCIIStringEncoding error:&error];
    //Get content from web using url variable are target.
    if (htmlData) {
        NSLog(@"%@", htmlData);
    } else {
        NSLog(@"%@", error);
    }
    return htmlData;
}
{% endhighlight %}

If you use NSLog(@"%@", htmlData) you should see the overwhelming chaos that an unfiltered web request can return. In the next post, part 2, we'll go over what you should with that data you just scraped. 
