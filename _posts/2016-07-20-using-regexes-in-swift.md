---
layout: post
title: Using Regular Expressions in Swift 
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

### Brief Overview
In part two we are switching from Objective-C to Swift. Before we get started let's take a moment to go over what a regular expression is. Regular expressions are a way of defining broader text patterns which you can then use to filter your data. In many cases regexes are a way to verify that user input in correct like a telephone number isn't just some random string but actually matches the format for a standard phone number. In our case, we want to look at the webdata we've gotten and find strings that conform to a basic “http://<www.whateveritfinds.com>”


### Implementing Our Code 
This is the regex pattern that gives us the basic pattern for the url: 

{% highlight swift linenos %}

"http?://([-\\w\\.]+)+(:\\d+)?(/([\\w/_\\.]*(\\?\\S+)?)?)?"

{% endhighlight %}

We will use the NSRegularExpression that Apple provides to us in the Foundation library. In my case I create a class SearchForURL: 

{% highlight swift linenos %}

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

In my ViewController I added the following lines to test my code:

{% highlight swift linenos %}

let newMatch = SearchForURL(input: "http://www.someurl.com ht  www.newcode.com http://www.newcode.com")
newMatch.search()

{% endhighlight %}

And I got the following output in response: 

![placeholder](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/screenshot.png "Raw web content")

Now we can clearly find the relevant data in our unorganized input.
