---
layout: post
title: Scraping The Web With Objective-C 
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

Using Objective-C to get and format web data is not an overly complicated process, but it isn’t something that should be done straight forward within a single method. In this example we will be using NSURL class to pass in a web url and return raw webpage and the NSRegularExpression class to make sense of that data. Part one will be primarily confined to the web request aspect of our task. In part two we will go over using NSRegularExpression to filter through the noise that the raw request returns so that you can find something useful.


### Brief Overview
When tackling this process let's first consider the steps involved in accomplishing our goal in this first part and setting ourselves up for the working with that data.. In this order (roughly) we need to do the following:

* Specify the url to scrape data from 
* Make our web request 
* Store what is returned to us in a data structure to be used later



### Extra Info
When you send request a URL on the web it looks roughly (most of the time) something like this (source objc.io post on tcp/ip http): 

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

While networking, TCP/IP, HTTP or the OSI model are not necessary prerequisites to be able to complete this exercise and probably are not something you will need to know the ins or outs of on a daily basis as a developer, it's definitely worthwhile to have a passing familiarity with them. For our purposes, NSURL comes standard in Apple's Foundation Library and it provides us with most of the functionality we need without requiring much input from the developer. 

### Raw Web Data
 To get started, let's get our data from the web by creating a method getWebContentFromURL that takes an NSString parameter, passedURL, and which returns an NSString of the raw web content. 


{% highlight objc linenos %}

 - (NSString*)getWebContentFromURL:(NSString *)passedURL {
    
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

If you did this correctly, you can use NSLog(@"%@", htmlData) to see the overwhelming chaos that is an unfiltered web request. 


Lovely isn't it?
![placeholder](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/screenshot.png "Raw web content")

In part 2, we'll go over what we need to do with that data to make sense of it in a way that a reasonable person could work with.
