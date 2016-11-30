---
layout: post
title: Experiments with ngrok and Twilio
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

### Brief Overview

While participating in the AT&T Mobile Hackathon, [Twilio](https://www.twilio.com) was kind enough to gift me $30 in credit towards any of their services.
I finally had the time over Thanksgiving to build something using Twilio. Just to get it out of the way, the example projects 
don’t work quite work out of the box. You need to do a fair amount of configuring and debugging and modification to get off the 
ground. The pod isn’t the easiest way to add the libraries to your project. You’ll be better off if you download the VoiceClient 
framework directly and drag it into your project. 

### Getting Started

Additionally, if you look at the Swift code for the basic Swift project that does not use[CallKit](https://github.com/twilio/voice-callkit-quickstart-swift), it is written in older style Swift, using semicolons at the end of lines and working with depreciated features from Foundation. I’d recommend skipping that version because it will take you down the iOS 10 [UserNotifications](https://developer.apple.com/reference/usernotifications) rabbit hole. Not that it is a bad rabbit hole to [dive down](https://realm.io/news/tryswift-ellen-shapiro-pushing-envelope-ios-10-notifications/), but if you want to get familiar with what is possible with Twilio, it’s a bit beside the point or off-topic.

### Using Flask

For the server, Twilio provides several server types that you can hook up as your backend. I chose the Python one, mainly because 
I have more experience with it, but obviously, you should choose the language that suits your preference. It’s incredibly simple to 
setup, and it uses the Python framework [Flask](http://flask.pocoo.org), which is easy to use and super fun. 

{% highlight  linenos %}

user:~# >> sudo pip install requirements.txt
user:~# >> python server.py

{% endhighlight %}

![Flask](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/flask.png)

### Meet ngrok

One last note before we move on the iOSie aspects, Twilio recommends that you use [ngrok](https://ngrok.com) to make a web URL from which you can tunnel into localhost. Ngrok is cool and will come in handy for a lot of projects going forward. I suggest using Homebrew to i
nstall it. 

{% highlight  linenos %}

user:~# >> brew cask install ngrok
user:~# >> ngrok http 5000

{% endhighlight %}

![ngrok](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/ngrok.png)

### Decisions

Now that we have our backend setup, it’s time to move into Apple land. To get started you are going to have to create a bunch of 
certificates using an Apple Developer account, so if you don’t have one, this would be a good time to decide whether or not you think
it is worth the $100 investment. Personally, I  believe that it pays off in the end. 

Okay, so this is the tedious part of the exercise. You’re going to need to create an App ID that has the “Push Notifications” service 
enabled. You’re also going to need to add a Provisioning Profile for that App ID. Finally, you’re going to need to to make a specialized 
VoIP Services Certificate to enable communication. This process is relatively straight forward to get done. 

### Wrap-up

 Once you’ve downloaded your VoIP, in your keychain access, you will need to export it to a [.p12](https://en.wikipedia.org/wiki/PKCS_12) file and extract the cert and private keys which you will need to add to your application setup on Twilio. Now you can go to your Twilio Dashboard and create a new Push Credential, pasting in the private and Cert keys when your configuring. Be sure to check ‘Sandbox’ in the options before you create the credentials. Below is the way that Twilio recommends you get the key and cert files. 
 
{% highlight  linenos %}

user:~# >> openssl pkcs12 -in ~/dev/certs/VoIPTwil.p12 -nocerts -out key.pem
user:~# >> openssl rsa -in key.pem -out key.pem
user:~# >> openssl pkcs12 -in ~/dev/certs/VoIPTwil.p12 -clcerts -nokeys -out cert.pem

{% endhighlight %}
 
Next post, part 2, we’ll dive into the workarounds needed to get the example app off the ground and running.



## Resources:

- [CallKit](https://developer.apple.com/reference/callkit)
- [Getting Started with Programmable Voice SDK](https://www.twilio.com/docs/api/voice-sdk/ios/getting-started)
