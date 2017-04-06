---
layout: post
title: Serving Ourselves! (With Node.js)
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

### Brief Overview

[Gist for code examples](https://gist.github.com/chriswebb09/59e96cd2e2c086b83540b5156d6c7513)

Networks go down. This was probably one of the more obvious statements that I’ve made. There are also times when you just don’t have 
access to a reliable internet connection for whatever reason. Knowing this fact, we can start going about mitigating the consequences 
ahead of time. 

One way to do this is to have some data (most likely JSON) stored locally that can be called upon whenever the need may arise. It doesn’t 
need to be entire databases worth, just enough to simulate proper functionality. In this post, I’ll show you how to construct a simple 
node server that you can use to serve dummy data to your app. 

### Let's get started!

If you don't have node installed on your computer you should do that now, because otherwise you won't be able to try your own code out. You can install node using the Mac packages manager [Homebrew](https://brew.sh/). When you've installed homebrew run the following commands:

{% highlight bash linenos %}

brew install node

{% endhighlight %}

To start let’s create our server directory and add our server index file and JSON file.

{% highlight bash linenos %}

mkdir server 
cd server 
touch index.js
touch movies.json 

{% endhighlight %}

Open your movies.json file and copy and paste your JSON into it and save it. 

Moving on to our server, let’s open the index.js file up in our text editor. You can see here at the top that a there are a bunch of global variables defined. 

### Overview of Require

Require is not part of standard Javascript. Node uses it to load modules. It’s similar to import in Swift.

npmjs.com defines require with the following:

_Node's require() is the de facto javascript dependency statement.
npm is the de facto javascript module manager.
require brings both of them to the browser.

We are using it to import the http, url, fs (filesystem) and path modules into our index.js file. We’re also going to specify the port that our server will run on, 3000. 

{% highlight js linenos %}
var http = require('http');
var url=require('url');
var fs = require('fs');
var path = require('path');
var port = 3000
{% endhighlight %}

### Accessing the Filesystem

To access the movie.json file, we need to create a file path variable with the location of it. We can do that by using our path module and 
calling join. ___dirname is the file path for the directory where index.js is located. Since movie.json is in the same directory we can tack
it on the end by using join and __dirname. 

{% highlight js linenos %}
var path = path.join(__dirname, 'movie.json');
{% endhighlight %}

To read it, we have to access the filesystem with the fs module. We have to pass in the file path of the file being read and an anonymous 
callback function that returns either error or data at the end (think of it as the completion handler that Swift uses.)  

{% highlight js linenos %}
fs.readFile(path, function(error, data) {
{% endhighlight %}

### URLs! 

Inside of this block, we can create a switch statement that switches on the URL used to access it. This allows you to simulate something 
that pagination from the network. If there is no error, we can return our data in our response.end call. 

{% highlight js linenos %}
  switch(url.parse(request.url).pathname) {
      case '/p1':
      if (!error) {
        response.end(data);
      } else {
          console.log(error);
        }
      case '/p2':
      if (!error) {
        response.end("Page 2");
      } else {
          console.log(error);
        }
      default:
        response.end("Default")
      }
    })
{% endhighlight %}

For now, just give the default case response a string, I used "Default, but you're free to use whatever catches your fancy. 

### Wrap-up

Node is a great and easy way to create a server to send your application some dummy data. To check that your server works, start it up in
your terminal by running the following in your project directory:

{% highlight bash linenos %}
node index.js
{% endhighlight %}

We can see if it works by creating a Playground in XCode and using and testing our APIClient 

{% highlight swift linenos %}

import UIKit
import PlaygroundSupport

PlaygroundPage.current.needsIndefiniteExecution = true

class APIClient {
    
    typealias JSON = [String: Any]
    
    // search functionality stuff 
}


APIClient.search(for: "new", page: 1) { movieData, error in
    print(movieData)
}


{% endhighlight %}

At the end of our index.js file we need to add the following. 

{% highlight js linenos %}
  }).listen(port, (error) => {
    if (error) {
      console.log('error ${ error }')
    }
    console.log('listening on ${ port }')
  });
{% endhighlight %}

I've posted the Swift api call playground and index.js in a gist which I will link to.

[index.js](https://gist.github.com/chriswebb09/59e96cd2e2c086b83540b5156d6c7513#file-index-js)
[APIClientPlayground.swift](https://gist.github.com/chriswebb09/59e96cd2e2c086b83540b5156d6c7513#file-apiclientplayground-swift)


* [npm documentation for require](https://www.npmjs.com/package/require)
* [Stackoverflow for anonymous functions Javascript](http://stackoverflow.com/questions/9901082/what-is-this-javascript-require)
* [Stackoverflow for require](http://stackoverflow.com/questions/9901082/what-is-this-javascript-require)
