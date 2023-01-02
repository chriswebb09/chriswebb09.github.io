---
title: "Complexity and Big-O Notation"
layout: post
date: 2017-05-11 01:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Complexity
- Swift
- Algorithm
category: blog
author: chriswebb
description: "Complexity and Big-O Notation"
---


![Complexity graph](https://i.stack.imgur.com/Aq09a.png)

### Introduction

In this post, I will touch on **complexity** and **Big O Notation**. The term complexity as it relates to programming doesn't necessarily mean one thing. There are different types of computational complexity. The two most common types of complexity are time complexity - which is the number of operations in regards to input and space complexity, which is the number of places used for storage in a given operation in regards to its input. For simplicities sake, in this post, I will be referring to time complexity.  

### Asymptotic Analysis

"_Asymptotic analysis is the process of describing the efficiency of algorithms as their input size (n) grows._"

 **-Swift Algorithms and Data Structures**

In programming, asymptotic analysis is generally described using Big O Notation.

### Worst case scenario

When referring to the worst-case scenario with complexity, we are referring to an input that causes maximum complexity. The difference between a best/worst case scenario for complexity may be significant. The difference between best case scenario and worst scenario for searching a hash table can go from constant O(1) time to linear O(n) time. If you are searching for an element in an array by iterating over each item and that element is the first item in the array, you will have a pretty good runtime. It's another story if that item is the element in the array. Generally speaking, you don't want to pretend like every situation is going to give you the optimal time.

### O(1) - Constant Time

An O(1) operation’s complexity is constant regardless of the number of inputs. Accessing the first element in an array will always be O(1). It is the same for an array with 1000 elements as it is for an array with 10.  In fact accessing any element in your array by its index should be a constant complexity operation regardless of whether it is the first item or the 1000th.

##### Examples of 0(1) operations:

* _Looking up element in a hashtable._
* _Adding a node to linked list_
* _Accessing an element in an array by index_


{% highlight swift %}

let arrOne = [1, 2, 3, 4, 5, 6]
arrOne[0]
arrOne[2]

{% endhighlight %}

Inserting node into linked list:

{% highlight swift %}

func append(value: T) {
    let newNode = Node(value: value)
    if let lastNode = last {
      newNode.previous = lastNode
      lastNode.next = newNode
    } else {
      head = newNode
    }
  }

{% endhighlight %}

### O(log n) - Logarithmic Time

When dealing with an O(log n) complexity operation, each cycle should reduce the complexity in relation to input. A prime example of this is a binary search. Each iteration halves the interval within which it searches.

##### Examples of 0(log n) operations:

* _Binary search_

![Binary search](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/220px-Binary_Search_Depiction.png)

{% highlight swift %}

let arrOne = [1, 2, 3, 4, 5, 6]

func binarySearch(_ inputArray: [Int], element: Int) -> Int? {

    var begin = 0
    var end = inputArray.count

    while begin < end {
        let mid = begin + (end - begin) / 2

        if inputArray[mid] == element {
            return mid
        } else if inputArray[mid] < element {
            begin = mid
        } else {
            end = mid
        }

    }

    return nil
}

binarySearch([1, 2, 3, 4, 5], element: 2)

{% endhighlight %}

### O(n) - Linear Time

An O(n) operation’s complexity scales linearly with the number of inputs. If you iterate through all the elements in an array, it is O(n), because the operation is dependent on the number of items in your collection. Going through 10 elements in an array requires ten cycles. If you multiply the array count by 10, the cycles increase to 100 or 10 times as much as ten elements.

##### Examples of 0(n) operations:

* _Traversing an array or linked list_
* _Removing a node from a specific index of a linked list_

_Remove Node At Index_:

{% highlight swift %}

func remove(node: Node) -> T {
    let prev = node.previous
    let next = node.next

    if let prev = prev {
      prev.next = next
    } else {
      head = next
    }
    next?.previous = prev

    node.previous = nil
    node.next = nil
    return node.value
  }

{% endhighlight %}

_Traverse Array_:

{% highlight swift %}

let arrOne = [1, 2, 3, 4, 5, 6]

for i in arrOne {
    print(i)
}

{% endhighlight %}

### O(n^2) - Quadratic Time

An O(n^2) operation’s complexity scales exponentially with the number of inputs. A simple example of an O(n^2) is a process with a loop within a loop. If you took an array with six elements and for each element of the array accessed nth element in the range of 0..<array.count you would access your array 36 times.  Your complexity is not scaling directly with input, but for input squared. A worst case scenario for a bubble sort is O(n^2).

##### Examples of 0(n^2) operations:

* _Traversing a 2D array_

{% highlight swift %}

var twoDimensionalArray: [[Int]] = [[0, 1], [2, 3]]

for i in 0..<twoDimensionalArray.count {
    for n in 0..<twoDimensionalArray[i].count {
        print(twoDimensionalArray[i][n])
    }
}

{% endhighlight %}


Sources:

* [Stack Overflow](http://stackoverflow.com/questions/1592649/examples-of-algorithms-which-has-o1-on-log-n-and-olog-n-complexities)
* [Wikipedia - Complexity Logarithm](https://en.wikipedia.org/wiki/Complex_logarithm)
* [Wikipedia - Binary search algorithm](https://en.wikipedia.org/wiki/Binary_search_algorithm)
* [Big O Cheat Sheet](http://bigocheatsheet.com/)
