---
title: "Data Structures - Stacks and Queues"
layout: post
date: 2018-01-30 22:48
image: /assets/images/stackqueue.png
headerImage: true
tag:
- iOS
- swift
- stack structure
- queue
category: blog
author: chriswebb
description: Adventures In Data Structures
---

#### Stacks and Queues

![](https://cdn-images-1.medium.com/max/1024/1*5kJdbFLjHufBfHlI5b84PA.png)

### Stack

A stack is a data structure that resembles an array but has a strict ordering logic to how items are added and removed from the stack. A stack is a **L** ast **I** n **F** irst **O** ut data structure.

If you stack a bunch of brick on top of each other when you want to take them all down again, which brick are you going to start with? The one at the top, the last brick that was placed on the pile. This concept unpins the LIFO data structures. That piece of data that is placed in a LIFO data structure is the first one that is retrieved. While a stack may superficially resemble an array in how it is organized, the structure of the ordering in a LIFO data structure is critical to its functioning correctly. Therefore, when you create a LIFO data structure, it is essential that you restrict how an element is accessed from&nbsp;it.

{% highlight swift %}
struct Stack<T> {

private var elements: [T] = []

init() {}

mutating func pop() -> T? {
return self.elements.popLast()
}


mutating func push(element: T){
self.elements.append(element)
}


func peek() -> T? {
return self.elements.last
}


func isEmpty() -> Bool {
return self.elements.isEmpty
}


var count: Int {
return self.elements.count
}

}
{% endhighlight %}

![](https://cdn-images-1.medium.com/max/1024/1*vEGC4lvFYZd1wkABzTqqWg.png)

### Queue

A queue is a **F** irst **I** n **F** irst **O** ut list data structure where items can only be inserted in the back and removed at the front. A queue can best be represented in the real world with how a line for movie tickets works. The first person who gets in line gets to buy their tickets first and everyone afterwards buys them in the order that they joined the line. In a FIFO data structure the first element added is the first element that is removed. Like with Stacks, the ordering of data removal from a queue is a critical to it functioning properly, so ensuring that access to the queue data is managed properly is&nbsp;key.

{% highlight swift %}
struct Queue<T> {

private var elements: [T] = []

var count: Int {
return elements.count
}

var isEmpty: Bool {
return elements.count == 0
}

var first: T? {
return elements.first
}

mutating func push(value: T) {
elements.append(value)
}

mutating func pop() -> T {
return elements.removeFirst()
}
}
{% endhighlight %}
