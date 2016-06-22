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



### What's going on behind the scenes when you surf the web...
Let's briefly go over what happens when you type in a url in your web browser and hit return. Without going into much detail, broadly speaking, your computer breaks down your your attempt to visit a web address into an HTTP GET request which is then packaged within TCP/IP and sent out into big wide internet as packets. This glosses over, ignores or is flat out wrong in many cases, I know. Networking is an incredibly complicated subject, with massive books devoted to teaching it. If you are at all concerned you can go buy one of those books. For the purposes of this example, we're going to be simplifying things. 

When you send request a URL on the web it looks roughly (most of the time) something like this: 

{% highlight  linenos %}

// ➜ curl -v http://flatironschool.com
* Rebuilt URL to: http://flatironschool.com/
*   Trying 104.236.44.135...
* Connected to flatironschool.com (104.236.44.135) port 80 (#0)

{% endhighlight %}

*HTTP GET request*

{% highlight http linenos %}

GET / HTTP/1.1
Host: flatironschool.com
User-Agent: curl/7.43.0
Accept: */*

{% endhighlight %}


*HTTP response 200 - everything is a-okay*

{% highlight http linenos %}

HTTP/1.1 200 OK
Server: nginx/1.8.0
Date: Tue, 21 Jun 2016 00:03:52 GMT
Content-Type: text/html
Content-Length: 37044
Last-Modified: Thu, 09 Jun 2016 14:17:29 GMT
Connection: keep-alive
ETag: "57597a79-90b4"
Accept-Ranges: bytes

{% endhighlight %}

While networking, TCP/IP, HTTP or the OSI model are not necessary prerequisites to be able to complete this exercise and probably are not something you will need to know the ins or outs of on a daily basis as a developer, it is definitely worthwhile to have a passing familiarity with them.

For our purposes, NSURL comes standard in Apple's Foundation Library and it provides us with most of the functionality we need without requiring much input from the developer. 

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


### Lovely isn't it?

![placeholder](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/screenshot.png "Raw web content")

In **Part 2**, we'll go over what we need to do with that data to make sense of it in a way that a reasonable person could work with.
