---
layout: post
title: Serving Ourselves! (With Node.js)
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

### Brief Overview

Networks go down. This was probably one of the more obvious statements that I’ve made. There are also times when you just don’t have 
access to a reliable internet connection for whatever reason. Knowing this fact, we can start going about mitigating the consequences 
ahead of time. 

One way to do this is to have some data (most likely JSON) stored locally that can be called upon whenever the need may arise. It doesn’t 
need to be entire databases worth, just enough to simulate proper functionality. In this post, I’ll show you how to construct a simple 
node server that you can use to serve dummy data to your app. 

### Let's get started!

To start let’s create our server directory and add our server index file and JSON file.

{% highlight bash linenos %}

mkdir server 
cd server 
touch index.js
touch movies.json 

{% endhighlight %}

Open your movies.json file and copy and paste your JSON into it and save it. 

Moving on to our server, let’s open the index.js file up in our text editor. You can see here at the top that a there are a bunch of global
variables defined. 

### Overview of Require

Require is not part of standard Javascript. Node uses it to load modules. It’s similar to import in Swift.  We are using it to import the 
http, url, fs (filesystem) and path modules into our index.js file. We’re also going to specify the port that our server will run on, 3000. 

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
{% endhighlight %}

### Wrap-up


