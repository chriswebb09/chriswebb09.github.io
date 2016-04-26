---
layout: post
title: New Development Blog
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

Well, it looks as if I am enrolling in the Flatiron School iOS immersive program. I'm going to use this place to document my experience as best as I can. Feel free to follow along.

So far, my only experience with the Flatiron program has their prerequisite work that must complete during the application process. Given that you can access it for free regardless of whether you are accepted to their program, I find it pretty useful and informative.  Even if you're not not 100% percent sure you are ready to apply, but you're looking to get started on Objective-C programming or Ruby web development, I recommend checking out the pre-work courses. Who knows, you might even enjoy it enough to send in an application.

You can check it their online pre-work and education here:

* [Flation School's Learn Co](https://learn.co)

This is an example of a pretty basic exercise. The work doesn't get too complicated, it mainly familiarizes you with concepts enough so that you can a. do the coding exercise to discuss for the technical interview and b. have enough knowledge to hit the ground running on day one should you be accepted.  Since I'm doing the iOS immersive the code is in Objective-C.

{% highlight objc linenos %}

- (NSString *)stringByReversingString:(NSString *)string{
    NSString *result = @"";

    for (NSUInteger i = [string length]; i > 0; i --) {
        NSUInteger index = i - 1;
        unichar c = [string characterAtIndex:index];
        result = [result stringByAppendingFormat:@"%C", c];
    }

    return result;
}

{% endhighlight %}

Here is a similar outcome from a block of code using the Python programming language:

{% highlight python linenos %}

def string_by_reversing_string(string):
    return string[::-1]

{% endhighlight %}
