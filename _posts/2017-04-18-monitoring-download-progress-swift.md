---
title: "Tracking Download Progress With Swift"
layout: post
date: 2017-04-18 01:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Networking
- iOS
- Data
category: blog
author: chriswebb
description: "Tracking Download Progress With Swift"
---


![Loading...](https://raw.githubusercontent.com/chriswebb09/Musicly/master/Assets/example.gif)


### Brief Overview

[Example Gist](https://gist.github.com/chriswebb09/dff9d5bd0964d817ac22d75d092733ba)

Recently I’ve been interested in building something that streams media. So last week, I started an example project, which streams ma4 music files from Apple’s iTunes Preview API. It’s been a fascinating learning experience. If you are interested, you can check it out here: [Musicly](https://github.com/chriswebb09/Musicly). The gif at the top of this post is a recording of the project in use.

In the process, I managed to touch some features in iOS that I had I been interested in looking at, but hadn't had the chance to use. I’ll be posting some examples of those features for the next few posts. To start off with, I want to go over monitoring the progress of a download in your application from both a data and UI perspective.

The example project for this post, which you can find in the gist link at the top, looks like this:

![Example](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/examplerecording.gif)

### Brief Overview Of URLSession

Before we get started, let's review some of the features that Apple provides for managing network requests. The [URL Loading System](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/URLLoadingSystem/URLLoadingSystem.html) is a set or 'family' of classes that Apple provides for dealing with URLs and accessing data on the internet.

__Apple describes the URL Loading System as__:

_The URL loading system is a set of classes and protocols that allow your app to access content referenced by a URL. At the heart of this technology is the NSURL class, which lets your app manipulate URLs and the resources they refer to._

One of the most common ways it is used is working with the URL class. This allows us to access resources over the network as well as ones that are stored locally in the filesystem.

{% highlight swift %}

let url = URL(string: "http://www.google.com")

{% endhighlight %}

One of the most useful sets of functionality is encapsulated within the URLSession class, and its associated delegates. This suite of functions gives us the ability to manipulate how our application interacts with the data it wants to access over the network.

{% highlight swift %}

var defaultSession = URLSession(configuration: .default)

{% endhighlight %}

This isn't the whole story. A lot the magic that makes this work is provided by Apple and happens under the hood, it's a higher-level abstraction of how iOS deals with networking tasks.

### Getting Started

To start with, let’s go over monitoring the progress of a download. This can be done visually with a progress bar or by giving a readout on a text label of the percentage complete.

{% highlight swift %}

@IBOutlet weak var progressView: UIProgressView!
@IBOutlet weak var downloadProgressLabel: UILabel!

{% endhighlight %}

To access the data, we’ll need to make our API client conform to URLSessionDownloadDelegate. This adventure will take us into the land of the Objective-C runtime. What this means in practical terms is that we will have our APIClient class to be a subclass of NSObject.

{% highlight swift %}

final class iTunesAPIClient: NSObject {
    // Network stuff
}

{% endhighlight %}

### Side Note

So far I've briefly touched on the topic of networking. The subject is immense, and this doesn't ever scratch the surface of what there is to learn about it. In fact, this is the opening sentence to Apple's 'About Networking' in 'Networking Overview':

_The world of networking is complex._

It's a bit of an understatement but it illustrates my point. Apple has it's rundown of the aspects of networking that it believes are relevant to developers in their ecosystem. [You can find that guide here](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/NetworkingConcepts/Introduction/Introduction.html).

### URLSessionDelegate and URLSessionDownloadDelegate

Apple specifies two methods that are relevant to this project in URLSession and URLSessionDownloadDelegate. They are:

_urlSessionDidFinishEvents_

_didWriteData:totalBytesExpectedToWrite_

The first method allows us to run networking calls on a background session. The second method gives us the data we need to monitor the progress of our download.

{% highlight swift %}

extension iTunesAPIClient: URLSessionDelegate {
    internal func urlSessionDidFinishEvents(forBackgroundURLSession session: URLSession) {
         // Calls background session completion in AppDelegate
    }

    internal func urlSession(_ session: URLSession, downloadTask: URLSessionDownloadTask, didWriteData bytesWritten: Int64, totalBytesWritten: Int64,totalBytesExpectedToWrite: Int64) {
        // Gives you the URLSessionDownloadTask that is being executed
         // along with the total file length - totalBytesExpectedToWrite
         // and the current amount of data that has received up to this point - totalBytesWritten
   }
}

{% endhighlight %}

### Side Note on BackgroundSessions and AppDelegates

When use background sessions you need to account for situations where the task is completed when your application is no longer running. If your network request completes when your application is no long active, the OS will relaunch it. In our AppDelegate we can handle this sort of relaunch by add this method

{% highlight swift %}
func application(_ application: UIApplication, handleEventsForBackgroundURLSession identifier: String, completionHandler: @escaping () -> Void) {
    // Background completion called here.
}
{% endhighlight %}

We can then add an optional completion property to the AppDelegate which we can then access inside our APIClient.

{% highlight swift %}
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    var backgroundSessionCompletionHandler: (() -> Void)?

    // AppDelegate functionality.
 }
{% endhighlight %}

To call this completion we add this functionality to our APIClient:

{% highlight swift %}
extension iTunesAPIClient: URLSessionDelegate {

    internal func urlSessionDidFinishEvents(forBackgroundURLSession session: URLSession) {
        if let appDelegate = UIApplication.shared.delegate as? AppDelegate,
            let completionHandler = appDelegate.backgroundSessionCompletionHandler {
            appDelegate.backgroundSessionCompletionHandler = nil
            DispatchQueue.main.async {
                completionHandler()
            }
        }
    }

    // Other networking functionality
}
{% endhighlight %}

### Delegates, Sessions and Models, Oh My!

Let's create a model class for the state of our the data in between the final data object your class app uses and the URL.

{% highlight swift %}

final class Download {

    weak var delegate: DownloadDelegate?
    var url: String?
    var downloadTask: URLSessionDownloadTask?

    var progress: Float = 0.0 {
        didSet {
            updateProgress()
            if progress == 1 {
                downloadTask = nil
                print("File is done")
            }
        }
    }

    // Gives float for download progress - for delegate

    private func updateProgress() {
        if let task = downloadTask,
            let url = url {
            delegate?.downloadProgressUpdated(for: progress, for: url, task: task)
        }
    }

    init(url: String) {
        self.url = url
    }
}

{% endhighlight %}

As you might notice, this class has a delegate which is the intermediary between our APIClient, and it's presentation to the user. To create it, make as class-bound protocol called DownloadDelegate and add it as a weak property to our Download model class.

{% highlight swift %}

protocol DownloadDelegate: class {
    // Delegate method
}

{% endhighlight %}

This delegate specifies a method:

{% highlight swift %}

protocol DownloadDelegate: class {
    func downloadProgressUpdate(for progress: Float)
}

{% endhighlight %}

In our Download model we can call the downloadProgressUpdate delegate method in the following manner:

{% highlight swift %}

final class Download {

    weak var delegate: DownloadDelegate?

    // Other properties

    var progress: Float = 0.0 {
        didSet {
            updateProgress()
        }
    }

    // Gives float for download progress - for delegate

    private func updateProgress() {
        delegate?.downloadProgressUpdated(for: progress)
    }

    // Initialization
}

{% endhighlight %}

Now we can make our ViewController conform to the download delegate protocol. Since we're modifying the layout every time the delegate gets called, you will need to specify that this update happens on the main thread (otherwise your application will crash.)

{% highlight swift %}

extension ExampleViewController: DownloadDelegate {

    func downloadProgressUpdated(for progress: Float) {
        DispatchQueue.main.async {
            self.progressView.progress += progress
            self.downloadProgressLabel.text =  String(format: "%.1f%%", progress * 100)
        }
    }
}

{% endhighlight %}

Obviously this isn't the entire project, but it should be enough to get your started. If you want to see how everything is done, I've provided a link to a Gist at the top of this entry that has all the code you need to get up and running.

### Sources
* [Apple docs for URLSession](https://developer.apple.com/reference/foundation/urlsession)
* [Apple docs for URLSessionDelegate](https://developer.apple.com/reference/foundation/urlsessiondelegate)
* [Apple docs for URLSessionDownloadDelegate](https://developer.apple.com/reference/foundation/urlsessiondownloaddelegate)
* [Apple docs for URL Loading System](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/URLLoadingSystem/URLLoadingSystem.html)
* [Objc.io Article on TCP/IP and HTTP](https://www.objc.io/issues/10-syncing-data/ip-tcp-http/)
