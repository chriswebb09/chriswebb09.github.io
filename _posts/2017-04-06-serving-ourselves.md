---
layout: post
title: Serving Ourselves! (With Node.js)
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

![Node logo](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/Node.js_logo.png)

## Brief Overview

[Gist for code examples](https://gist.github.com/chriswebb09/59e96cd2e2c086b83540b5156d6c7513)

Networks go down. That is one of the more obvious statements that I’ve made on this blog. There are also times when you just don’t have access to a reliable internet connection for whatever reason. In short, it's one of those factors in development that you can lose control over. Knowing this, we can start going about mitigating the consequences ahead of time. 

One way to do this is to have some data (most likely JSON) stored locally that can be called upon whenever the need may arise. It doesn’t need to be entire databases worth, just enough to simulate proper functionality. In this post, I’ll show you how to construct a simple Node.js server that you can use to serve dummy data to your app. 

### Installation

If you don't have node installed on your machine you should do that now because otherwise, you can't run a node server. You can install node using the Mac package manager [Homebrew](https://brew.sh/). When you've installed Homebrew execute the following commands:

{% highlight bash linenos %}

brew install node

{% endhighlight %}

### Let's get started...with installations

To start let’s create our server directory and add our server index file and JSON file.

{% highlight bash linenos %}

mkdir server 
cd server 
touch index.js
touch movies.json 

{% endhighlight %}

Open your movies.json file and copy and paste your JSON into it and save it. For this example, I'm going to use the response from [OMDb API](http://www.omdbapi.com/). To get some JSON, copy and paste into your movie.json from the following page: [http://www.omdbapi.com/?s=Batman&page=2](http://www.omdbapi.com/?s=Batman&page=2)

## Let's Build Our Server

Moving on to our server, let’s open the index.js file up in our text editor. You can see that at the top of the file there are several of global variables that get defined. All but on of these variables uses something called require. 

### Overview of Require

Require is not part of standard Javascript. Node uses it to load modules. Importing in Swift is similar.

[npmjs.com](https://www.npmjs.com/package/require) defines require with the following:

_Node's require() is the de facto javascript dependency statement.
npm is the de facto javascript module manager.
require brings both of them to the browser._

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
calling join. `__dirname`  is the file path for the directory where index.js is located. Since movie.json is in the same directory we can tack it on the end by using join and `__dirname`. 

{% highlight js linenos %}
var path = path.join(__dirname, 'movie.json');
{% endhighlight %}

That should look something like: /Users/YourUsername/server/movie.json or some derivative of that depending on where your server directory is located. To read the JSON file at that location, we have to access the filesystem with the fs module and calling readFile. This takes in the path to the file being read and an anonymous function with the parameter error and data. The function that you're passing is a callback function that returns either error or data at the end (think of it as the completion handler that Swift uses.)   

{% highlight js linenos %}
fs.readFile(path, function(error, data) {
{% endhighlight %}

### URLs! 

Inside of this block we need to traverse a switch statement that does something depending on the URL that was used to access the server.
This switch statement can help with simulating pagination or different response type. If there is no error, we can return our data in our response.end call. 

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

## Wrap-up

Node is a great and easy way to create a server to send your application some dummy data. To check that your server works, start it up in
your terminal by running the following in your project directory:

{% highlight bash linenos %}
node index.js
{% endhighlight %}

If you get it right, your terminal should look something like this:

![Terminal](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/nodeserver.png)

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

---

##### Sources

* [npm documentation for require](https://www.npmjs.com/package/require)
* [Stackoverflow for anonymous functions Javascript](http://stackoverflow.com/questions/9901082/what-is-this-javascript-require)
* [Stackoverflow for require](http://stackoverflow.com/questions/9901082/what-is-this-javascript-require)
