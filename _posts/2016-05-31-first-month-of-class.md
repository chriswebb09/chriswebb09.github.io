---
layout: post
title: First Month of Classes
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

The Flatiron class is about to officially begin next week, but for the last two weeks I've been getting an idea of what it will be like by doing prework on campus. I'm pretty stoked, all the people seem awesome, the material is challenging and the campus/surrounding area is super nifty. On Wednesday, I'm moving into the new housing in Brooklyn. Hopefully everything will go well. So far the biggest challenge I've had is building a Swift to-do list app. I think I should spend more time learning the syntax and particulars of the language before making anything else in Swift because I'm not applying learning process effectively when building the app without the solid foundation. 

Some Swift code from one of the assignments:

{% highlight swift linenos %}

override func viewDidLoad() {
        super.viewDidLoad()
        if let htmlFile = NSBundle.mainBundle().pathForResource("BullsEye", ofType: "html") {
            if let htmlData = NSData(contentsOfFile: htmlFile) {
                let baseURL = NSURL(fileURLWithPath: NSBundle.mainBundle().bundlePath)
                webView.loadData(htmlData, MIMEType: "text/html", textEncodingName: "UTF-8", baseURL: baseURL)
            }
        }
}

{% endhighlight %}
