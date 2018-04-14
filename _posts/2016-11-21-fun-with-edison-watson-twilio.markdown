---
title: "Fun with Edison, Watson and Twilio"
layout: post
date: 2016-11-21 01:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Swift
- IBM Watson
- iOS
- Twilio
star: false
category: blog
author: chriswebb
description: Fun with Edison, Watson and Twilio
---


### IBM, Intel and Twilio

Sometimes it can be refreshing to step away from the familiar Appley-world that I find myself working in most of the time and try new things. This weekend I was able to take just such an opportunity while participating in the AT&T Mobile IOT Hackathon to explore some new technology. Some of the coolest explorations involved Intel’s Edison board, IBM’s Watson, server-side Swift with IBM's Bluemix and Kitura and Twilio's messaging platform.

### Working with Edison

Edison ended up being one of the biggest challenges. We decided to use it to transcribe sound to text for two reasons.
First: the external microphone add-on was more sensitive than the iPhone mic. Also using Edison was one of the criteria for the Hackathon.
Beyond that, it’s always interesting to explore new things.

![Edison Board](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/437f929f-bb3e-499f-b49b-aded7a3838d8-imageId%3D7796fe05-9f70-4a00-8303-e1ea5755cdec.png)


### ALSA for sound

After flashing a new install to the board, we ssh’d our way onto it and used the package manager, opkg, to install the linux sound library ALSA. After hooking the external microphone into the the board, we were able to record a wav file sound file by using the following command

{% highlight  line %}

root@device:~# arecord -f cd -c 1 -D hw:2,0 mono.wav

{% endhighlight %}

### Curl it up to the Watson cloud

Once the desired length of sound had been recorded, it is a matter of pressing control-c to exit and now a mono.wav sound file is saved onto the Edison board filesystem. In order to work with that sound file, you can use SFTP to download it onto your computer. Or you can do what we did, which is send it up to Watson for speech-to-text analysis.

{% highlight  line %}

root@device:~# curl -X POST -u "username":"password" --header "Content-Type: audio/wav" --data-binary @mono.wav "https://stream.watsonplatform.net/speech-to-text/api/v1/recognize?timestamps=true&word_alternatives_threshold=0.9&continuous=true" > datafile.json

{% endhighlight %}

This saves the JSON response from the Watson analysis into a JSON file on the Edison’s filesystem.
