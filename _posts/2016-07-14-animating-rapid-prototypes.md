---
layout: post
title: Creating Rapid Prototypes In Swift
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

There are multiple ways to prototype, from static to programatic. The best prototypes are the ones that mimic the final product without requiring the backend that will power the final product and only taking a fraction of the development time. Using animations we can give the impression of logical behavior to images. This creates an illusion of functionality.   

This post will focus on transform animations to create a simulated pop out menu. This is a view that appears over your other content. At the same time we dim the background so that the focus is completely on the new popover element. Before you animate your image you create the initial properties that you will transform from. Inside your animation code block you create a second set of properties that your animation will transition into. 

{% highlight swift linenos %}

@IBAction func userButtonDidTouch(sender: AnyObject) {
        popoverView.hidden = false
        showBackgroundMaskViewButton()
        
        let scaleFirst = CGAffineTransformMakeScale(0.3, 0.3)
        let translateFirst = CGAffineTransformMakeTranslation(50.0, -50.0)
        // The second parameter refers to the starting point, the first parameter refers to the ending point.
        popoverView.transform = CGAffineTransformConcat(scaleFirst, translateFirst)
        popoverView.alpha = 0
        
        UIView.animateWithDuration(0.5, delay: 0, usingSpringWithDamping: 0.7, initialSpringVelocity: 0.5, options: [], animations: {
            let scaleSecond = CGAffineTransformMakeScale(1.0, 1.0)
            let translateSecond = CGAffineTransformMakeTranslation(0, 0)
            self.popoverView.transform = CGAffineTransformConcat(scaleSecond, translateSecond)
            self.popoverView.alpha = 1
        }, completion: nil)
    }
    
{% endhighlight %}

    
Inside your animation code block you create a second set of properties that your animation will transition into. The animation function takes in a parameter which you use to specify and actual type of transformation. Finally you set the a new alpha value to 1 from zero. This makes the image visible. These all come together to create the feeling that your popoverview is growing from the corner of your screen. When setting up your popoverview, you will most likely want to find a way to dismiss it. showBackgroundMask button you can then make the background outside of your popoverview into one large button which then dismisses your popover image once 
