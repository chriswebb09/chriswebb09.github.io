---
layout: post
title: Operation and OperationQueue
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

![GCD](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/train-symbol.jpg)

## Diving In
Today I’m going to take a look as Operation, what it is exactly, and what you can do with it.  Hopefully, I can shed some light on it 
if you’ve been confused. With the advent of Swift 3 and “the great renaminging,” much of the Swift code written from now onward will no
longer have a large dose of NS prefixed classes within them. This the case with [Operation](https://developer.apple.com/reference/foundation/operation) and [OperationQueue](https://developer.apple.com/reference/foundation/operationqueue) in Swift.

### Operation
Operation, the abstract class that was formally known as NSOperation, is the tool that Apple provides to developers for encapsulating 
the data and behaviors that make up the execution of a single task, regardless of complexity. The most important aspect here is that your 
operation has states which clearly demarcate the stages of the process. 

### OperationQueue
Similarly, OperationQueue is what Apple uses to encapsulate work. While a task and work are intimately related, they are not the same thing. Think of Operation almost like a unit that makes up an OperationQueue. Filing papers may be a single task, but most likely, the work you do for your job encompasses more than that single task.  Or take for instance going to the supermarket. You go to the store, and getting each item on your list could be plausibly broken down in single operation package. Stepping out from there, your might have to take your laundry and go to the bank, etc. Now your trip to the supermarket was one Operation in a set of Operations called “errands” While each errand is an operation, it encapsulated inside the OperationQueue errands.

 The important thing here is the clear delineation between Operations. You walk to the dairy isle and which kicks off the set of behaviors 
 and information that make up the "get milk" task. When you pay for your groceries, your errand “Get groceries” is finished. etc. If it
 can be plausibly be explained as a single task, then you can wrap it an NSOperation subclass.  If there are a set of interrelated 
 Operations,throw them into an OperationQueue. 

### Appley-stuff
OperationQueue and Operation are higher level abstractions from Apple’s [Grand Central Dispatch](https://developer.apple.com/reference/dispatch). What this means is that they are built on 
top of the Grand Central Dispatch codebase. So when you implement an Operation, you are using GCD. 

As a higher level abstraction, it is subject to less change when Apple tweaks the underlying code in as it progresses through of versions 
numbers. In general, you should try to use the highest level abstraction that you can, because it means you will most likely have to change less of your code to work with newer versions. 

## Wrap Up
GCD is ideal for quickly dispatching queues to their proper threads like when you need to do a quick update on your UI. But, if you have a 
self-contained set of functionality and data you might be better off creating an Operation subclass. You can then have more fine-grained control over the execution of your Operation than you would by using GCD by itself.  

In your custom Operation you can implement a custom version of the start() and cancel() methods to perform a unique process for the 
initialization and ending of your sequence. As a simple example, you could log every time the operation gets canceled.  An example of where you would use Operation is for downloading an image. Network tasks are full of behaviors and data that you can plausibly encapsulate in a single operation. If you wanted to download several pictures, they could be added to their own image download OperationQueue. 

![puppyimg](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/5ac1apZ.jpg)

#### Link to gist with example code will be added in near future.

### Sources 
* [https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationObjects/OperationObjects.html#//apple_ref/doc/uid/TP40008091-CH101-SW36](https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationObjects/OperationObjects.html#//apple_ref/doc/uid/TP40008091-CH101-SW36)

* [https://developer.apple.com/videos/play/wwdc2016/720/](https://developer.apple.com/videos/play/wwdc2016/720/)


* [https://medium.com/@raulriera/nsoperations-nsoperationqueue-oh-my-88b707f9ba2e#.kf38sa61r](https://medium.com/@raulriera/nsoperations-nsoperationqueue-oh-my-88b707f9ba2e#.kf38sa61r)
