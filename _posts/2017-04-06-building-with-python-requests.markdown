---
title: "Building With Python Requests"
layout: post
date: 2017-04-06 01:48
image: "assets/image/node.png"
headerImage: true
tag:
- Python
- Requests
- HTTP
category: blog
author: chriswebb
description: "Basic server with NodeJS"
---

![requests-library-logo](https://raw.githubusercontent.com/kennethreitz/requests/master/docs/_static/requests-logo-small.png)

## Overview

[Gist to example code](https://gist.github.com/chriswebb09/8dbd3eadbe75ca37bcb54465e29f5749)

If you have ever wanted to build a web-crawler, Python is a great language to use too. Python concise, widely installed across platforms and it has a multitude of networking and parsing related libraries for you to leverage. This post will go over the beginnings of building a web crawler, the functionality that scrapes URLs from sites. For this example, I’m going to be using the [Requests](http://docs.python-requests.org/en/master/), [sys](https://docs.python.org/2/library/sys.html) and [re](https://docs.python.org/2/library/re.html) libraries.

### Installing Prerequisites

If you don’t have lots of experience working HTTP or Python, Requests is a great library to get started. If the Requests library isn't already installed on your machine go ahead a do that using pip:

{% highlight bash %}
pip install requests
{% endhighlight %}


## Python Modules

### Requests

The people behind [Requests](http://docs.python-requests.org/en/master/) call it HTTP for Humans, and that’s precisely what it is. It’s one the of most beautiful works of code that I have come across. It is eminently readable and logically constructed. There's a word to describe this in the Python community: Pythonic

A good definition for Pythonic that I've come across is:

_Exploiting the features of the Python language to produce code that is clear, concise and maintainable.Pythonic means code that doesn't just get the syntax right but that follows the conventions of the Python community and uses the language in the way it is intended to be used._

### sys

I want to setup our Python script so that we can specify our url directly from the commandline like so:

{% highlight bash %}
python linkcrawler.py www.google.com
{% endhighlight %}

The documentation in the Python standard library for [sys](https://docs.python.org/2/library/sys.html) calls it: System-specific parameters and functions In this case, we’re going to use it to access the URL that gets entered when our file is called on the command line using the sys.argv command. The sys.argv method returns an array with strings for everything typed in after the word python. Each additional entry in into the command line gets its own index. So inputting sys.argv[0] would have the interpreter return to us ‘linkcrawler.py’ while sys.argv[1] would give us www.google.com.

### Classes in Python

Creating classes in Python is interesting slightly different than in Swift.

Python standard library definition for [classes](https://docs.python.org/2.7/tutorial/classes.html):

_Compared with other programming languages, Python’s class mechanism adds classes with a minimum of new syntax and semantics. It is a mixture of the class mechanisms found in C++ and Modula-3. Python classes provide all the standard features of Object Oriented Programming: the class inheritance mechanism allows multiple base classes, a derived class can override any methods of its base class or classes, and a method can call the method of a base class with the same name. Objects can contain arbitrary amounts and kinds of data. As is true for modules, classes partake of the dynamic nature of Python: they are created at runtime, and can be modified further after creation._

To define one, prefix your class names with the word class and put a colon at the end of the declaration. What makes classes in Python interesting is that you have to pass in self as a parameter in every function block. To give our class an init method we need to add '__init__(self)':

{% highlight py %}
def __init__(self):
        print(sys.argv[0])
        if sys.argv[1].startswith("http://") or sys.argv[1].startswith("https://"):
            self.url = sys.argv[1]
        else:
            self.url = "https://" + sys.argv[1]
{% endhighlight %}

Specifying self as a paramter is not optional, if you leave out the self, you will get this error:

{% highlight bash %}
TypeError: __init__() takes no arguments (1 given)
{% endhighlight %}

In the init method, we can instantiate our class properties. In this case, I gave the class a URL property:

{% highlight py %}
self.url = sys.argv[1]
{% endhighlight %}

This uses the sys.argv method that we talked about earlier to get the URL that is entered in when you run the file.

### Python Methods

The majority of the logic for this piece will go in a method I named request_resource.

{% highlight py %}
    def request_resource(self):
        r = requests.get(self.url)
        urls = re.findall('http[s]?://(?:[a-zA-Z]|[0-9]|[$-_@.&+]|[!*\(\),]|(?:%[0-9a-fA-F][0-9a-fA-F]))+', r.text)
        return urls
{% endhighlight %}

This method takes in self as a parameter as well. We set our variable r to the results of an HTTP GET request to the URL we specified initially.Using re.findall we look through r.text for text that matches URL patterns. These are stored in our URLs variable and returned at the end of the method.

### Python re Module

The Python re module is provided in the Python standard library and gives the language Perl-like regular expression patterns. Regular expressions are a way of specifying certain patterns within strings.

Wikipedia entry for Regular Expressions:
_A regular expression, regex or regexp[1] (sometimes called a rational expression) is, in theoretical computer science and formal language theory, a sequence of characters that define a search pattern.
Usually this pattern is then used by string searching algorithms for "find" or "find and replace" operations on strings._

A regular expression could match phone number or email address or in this case, URLs. This strange looking string is a regex pattern for matching text to URLs:

{% highlight py %}
"http[s]?://(?:[a-zA-Z]|[0-9]|[$-_@.&+]|[!*\(\),]|(?:%[0-9a-fA-F][0-9a-fA-F]))+"

{% endhighlight %}

## Wrapping Up

Now that we have that finished with our class we need to add a main function that will get called when our file is run directly from the command line. In main we will need to create an instance of our class, call our request_resource method and for now, just print out the URLs if they exist.

{% highlight py %}
def main():
    crawl = WebCrawler()
    new_data = crawl.request_resource()
    if new_data is not None:
        for url in new_data:
            print(url)
{% endhighlight %}

Once we have our main function figured out, we now need to ensure that it gets called when the file is run. We can add some
if __name__ magic to get this all setup correctly.  

{% highlight py %}
if __name__ == '__main__':
    main()
{% endhighlight %}

What we're specifying here is that if Python calls our file directly (the if __name__ == '__main__':) it should run our file's main function. This is different than if it is imported into another file and called and it needs specification so we can run it from the command line.

If you got everything right it should look something like:

![Example](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/pythoncrawler.png)

---

##### Resources

[Python Standard Library Documentation for re](https://docs.python.org/2/library/re.html)
[Requests Library documentation](http://docs.python-requests.org/en/master/)
[Kenneth Reitz github](https://github.com/kennethreitz/requests)
[Python Standard Library Documentation for sys](https://docs.python.org/2/library/sys.html)
[StackOverflow for Pythonic](http://stackoverflow.com/questions/25011078/what-does-pythonic-mean)
[Python Standard Library for Class](https://docs.python.org/2.7/tutorial/classes.html)
