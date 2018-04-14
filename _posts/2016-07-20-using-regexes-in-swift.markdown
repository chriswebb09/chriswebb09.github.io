---
title: "Using Regexes In Swift"
layout: post
date: 2016-07-20 01:48
image: /assets/images/ios_logo_black.png
headerImage: true
tag:
- Swift
- regular expressions
- iOS
star: false
category: blog
author: chriswebb
description: "Regular expressions"
---

### Brief Overview
In part two we are going to switch from writing our code in Objective-C to writing in Swift. Before we get started, let's take a moment to go over what regular expressions are. Regular expressions are a way of defining broader text patterns, like phone numbers or urls, which you can then use to filter a larger block of text data and return only the relevant parts. Regexs are commonly used as a way to verify that a user input is in the correct format. In our case, we want to look at the webdata we retrieved earlier to find strings that conform to a basic http://www.whateveritfinds.com


### Implementing Our Code
This is the regex pattern that gives us the basic pattern for the url:

{% highlight swift %}

"http?://([-\\w\\.]+)+(:\\d+)?(/([\\w/_\\.]*(\\?\\S+)?)?)?"

{% endhighlight %}

Apple provides us with a NSRegularExpression class in the Foundation Library. It simplifies the whole process for us immensely. The way I implemented the search was in a class called SearchForURL:

{% highlight swift %}

class SearchForURL {
    let input: String
    let pattern: String
    init(input: String) {
        self.input = input
        self.pattern = "http?://([-\\w\\.]+)+(:\\d+)?(/([\\w/_\\.]*(\\?\\S+)?)?)?"
    }

    func search() {
        do {
            let regex = try NSRegularExpression(pattern:self.pattern, options: NSRegularExpressionOptions.CaseInsensitive)
            let matches = regex.matchesInString(input, options: [], range: NSRange(location: 0, length: input.utf16.count))
            for match in matches {
                let range = match.rangeAtIndex(1)
                print(input.substringWithRange(range.rangeForString(self.input)!))
            }
            if let match = matches.first {
                let range = match.rangeAtIndex(1)
                if let urlRange = range.rangeForString(self.input) {
                    let url = input.substringWithRange(urlRange)
                    print(url)

                }
            }
        }
        catch {
            print("could not complete")
        }    
    }

}

{% endhighlight %}

To check whether my implementation works, in my ViewController I added the following lines of code:

{% highlight swift %}

let newMatch = SearchForURL(input: "http://www.someurl.com ht  www.newcode.com http://www.newcode.com")
newMatch.search()

{% endhighlight %}

And I got the following output in response:

![placeholder](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/screenshot-2.png "Filtered Web Content")

Now we can clearly find the relevant data in our unorganized input.
