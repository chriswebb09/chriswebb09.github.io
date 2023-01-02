---
title: "Serving Ourselves! - With Node.js"
layout: post
date: 2017-04-06 01:48
image: "/assets/images/node.png"
headerImage: true
tag:
- Javascript
- Node
- Server
category: blog
author: chriswebb
description: "Basic server with NodeJS"
---

# Brief Overview

Networks go down. That is one of the more obvious statements that I’ve made on this blog. There are also times when you just don’t have access to a reliable internet connection for whatever reason. In short, using data from the internet brings into play factors that you don’t have one hundred percent control over. Knowing this, we can start going about mitigating the consequences ahead of time. One way to do this is to have some data (most likely JSON) stored locally that can be called upon whenever the need may arise. It doesn’t need to be entire databases worth, just enough to simulate proper functionality. In this post, I’ll show you how to construct a simple Node.js server that you can use to serve dummy data to your app.

I got the idea to write this post recently when I was moving. The place where I was staying temporarily did not have internet hooked up, and I had gone way over the data allotment on my mobile plan for the month and the phone company was throttling my connection. Having a server send my application ‘dummy data’ would have been a quick fix for my situation. Even if you don’t have a specific reason right now, working with node is a handy skill to have and something that could prove useful to you further down the line.

Let’s get started…with installations
If you don’t have node installed on your machine you should do that now because otherwise, you can’t run a node server. You can install node using the Mac package manager Homebrew. When you’ve installed Homebrew execute the following commands:

brew install node

## Setup

To start let’s create our server directory and add our server index file and JSON file.

mkdir server
cd server
touch index.js
touch movies.json

Open your movies.json file and copy and paste your JSON into it and save it. For this example, I’m going to use the response from OMDb API. To get some JSON, copy and paste into your movie.json from the following page: http://www.omdbapi.com/?s=Batman&page=2

## Let’s Build Our Server

Moving on to our server, let’s open the index.js file up in our text editor. You can see that at the top of the file there are several of global variables that get defined. All but on of these variables uses something called require.

## Overview of Require

Require is not part of standard Javascript. Node uses it to load modules. Importing in Swift is similar.

npmjs.com defines require with the following:

Node’s require() is the de facto javascript dependency statement.
npm is the de facto javascript module manager.
require brings both of them to the browser.

We are using it to import the http, url, fs (filesystem) and path modules into our index.js file. We’re also going to specify the port that our server will run on, 3000.

{% highlight js %}

var http = require('http');
var url=require('url');
var fs = require('fs');
var path = require('path');
var port = 3000

{% endhighlight %}

## Accessing the Filesystem
To access the movie.json file, we need to create a file path variable with the location of it. We can do that by using our path module and
calling join. "_dirname" is the file path for the directory where index.js is located. Since movie.json is in the same directory we can tack it on the end by using join and '_dirname'.

{% highlight js %}

var path = path.join(_dirname, 'movie.json');

{% endhighlight %}

That should look something like: /Users/YourUsername/server/movie.json or some derivative of that depending on where your server directory is located. To read the JSON file at that location, we have to access the filesystem with the fs module and calling readFile. This takes in the path to the file being read and an anonymous function with the parameter error and data. The function that you’re passing is a callback function that returns either error or data at the end (think of it as the completion handler that Swift uses.)

{% highlight js %}

fs.readFile(path, function(error, data) {
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
}
{% endhighlight %}

## URLs!
Inside of this block we need to traverse a switch statement that does something depending on the URL that was used to access the server.
This switch statement can help with simulating pagination or different response type. If there is no error, we can return our data in our response.end call.

{% highlight js %}

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

For now, just give the default case response a string, I used “Default, but you’re free to use whatever catches your fancy.

Wrap-up
Node is a great and easy way to create a server to send your application some dummy data. To check that your server works, start it up in
your terminal by running the following in your project directory:

node index.js

If you get it right, your terminal should look something like this:

We can see if it works by creating a Playground in XCode and using and testing our APIClient

{% highlight swift %}

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

At the end of our index.js file we need to add the following:

{% highlight js %}

}).listen(port, (error) => {
  if (error) {
    console.log('error ${ error }')
  }
  console.log('listening on ${ port }')
});


{% endhighlight %}
