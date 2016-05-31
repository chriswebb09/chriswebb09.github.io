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

func updateOperation() {
        operationalIndex = Int(arc4random_uniform(4))
        switch operationalIndex {
        case 1 :
            operationLabel.text = String("+")
            operational = 1
        case 2 :
            operationLabel.text = String("-")
            operational = 2
        case 3 :
            operationLabel.text = String("÷")
            operational = 3
        case 4 :
            operationLabel.text = String("x")
            operational = 4
        default :
            operationLabel.text = String("+")
            operational = 1
        }
    }

{% endhighlight %}
