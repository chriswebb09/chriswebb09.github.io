---
layout: post
title: The Case Of Dr. Swift and Mr. Objc
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

## Diving In
In Swift, you have the option of invoking the Objective-C runtime by using two different keywords: @objc and dynamic. This post endeavors to explain some of what these keywords do and how they differ. 

Apple describes the Objective-C runtime as follows:  “The runtime system acts as a kind of operating system for the Objective-C language; it’s what makes the language work” Which is sort of like BMW saying the “engine makes the wheels spin.”

[There is a lengthy write-up on the topic of the Objective-C runtime on developer.apple.com](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html) if you care to read it. “In Objective-C, messages aren’t bound to method implementations until runtime. The compiler converts a message expression, into a call to a messaging function, objc_msgSend.” In Swift, the compiler can do optimizations that make it incompatible with the Objective-C runtime.  These include method inlining and devirtualization. Wheneven it can, Swift will optimize by using static dispatching, which is the fastest implementation for method dispatch. 

![neutralness](http://i.imgur.com/GCuxx.jpg)

## Inlining and Virtualization
Inlining is when the compiler replaces a method/function call with the actual implementation. Essentially, it hardcodes. This can remove the computational overhead of the more dynamic method calls, but it comes at the expense of hardcoding the implementation. Your code will now take up more space. It’s a tradeoff between optimizing for space and optimizing for performance. 

Without getting too sidetracked, virtualization is when your program figures out which implementation of the code it is going to use at runtime, instead of compile time. The rabbit hole goes deep here, and is beyond the scope of what I want to cover. When you use "@objc" type annotation, you are making the modified code available to the Objective-C runtime. This does not, however, guarantee that the compiler, in its wisdom, will decide to use that option. 

Think of it conceptually as sort of like an optional for runtime, in the sense that it could be this, or it could be that, and we’ll let fate sort it all out later. Much in the same sense that you can force unwrap an optional, you can also force the Objective-C runtime by using the type annotation "dynamic. This ensures that it is dynamically dispatched in the Objective-C runtime using objc_msgSend(). If you want to use dynamic you will need to have the Foundation library imported into your file.  

{% highlight swift linenos %}@objc var = ["A", "B", "C"]
dynamic var = ["A", "B", "C"]
{% endhighlight %}

## Wrap Up
Why might you want to do this? That's an interesting question. As Apple notes, even when running Objective-C you should rarely need to interact with the Objective-C runtime. Using it can be handy to implement things like method swizzling, to modify existing code that you would otherwise have trouble accessing. 

[NSHipster](http://nshipster.com/swift-objc-runtime/): "Method swizzling lets you swap the implementations of two methods, essentially overriding an existing method with your own while keeping the original around." 

[In Apple’s write-up for Swift optimization, they recommend that you do this as little as possible because it will make your code slower.](https://developer.apple.com/swift/blog/?id=27) The drawback to this way thinking is that, down the line, should someone else build something on top of your code, if you haven’t thought ahead and implemented it, it limits their capacity to change it as they might want. And it goes without saying that:

![neutralness](https://i.imgur.com/jRF1hwd.jpg)


Apparently, there is some rivalry within the Apple programming universe between [Swifters and Objective-Cers.](https://ashfurrow.com/blog/contempt-in-swift/) If you look around at blog posts, there is a fair amount of snark leveled towards eachother regarding this topic. I don’t know, and frankly, I couldn’t care any less what programming language you prefer. Ultimately, use whatever works best for you.  



### Sources
 
- [https://www.uraimo.com/2015/10/23/effective-method-swizzling-with-swift/](https://www.uraimo.com/2015/10/23/effective-method-swizzling-with-swift/)

- [https://en.wikipedia.org/wiki/Inline_expansion](https://en.wikipedia.org/wiki/Inline_expansion)

- [https://github.com/apple/swift/blob/master/docs/proposals/ObjCInteroperation.rst](https://github.com/apple/swift/blob/master/docs/proposals/ObjCInteroperation.rst)

- [http://www.drdobbs.com/class-hierarchy-graphs-function-devirtua/184401938](http://www.drdobbs.com/class-hierarchy-graphs-function-devirtua/184401938)

- [https://en.wikipedia.org/wiki/Virtual_function](https://en.wikipedia.org/wiki/Virtual_function)

- [https://www.raizlabs.com/dev/2016/12/swift-method-dispatch/#file-message-swift](https://www.raizlabs.com/dev/2016/12/swift-method-dispatch/#file-message-swift)
