---
layout: post
title: Making A Hash Table In Swift : Part One
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

# Adventures In Data Structures

## Overview
This post will continue my series on data structures and will touch on the concepts behind and implementation of hash tables. Hash tables
are one of the most commonly used data structures in development. In fact, even if you haven’t heard the term before, you have probably
used them in one form or another.

## O(1) Complexity
One of the reasons you find hash tables used so often is that they are very efficient. The time complexities for a hash table search, item 
insertion and item deletion are on average O(1). This means the time operation time stays constant regardless of the size of the input. As 
you can imagine, this can come in quite handy with large or expanding data sets.

## Key Value Pairing
Key-value pairing is found throughout Swift and Apple’s frameworks/libraries.

*Apple Docs*:
_A key-value pair is a combination of a key and a value._

One place you come across key-value pairing is when using dictionaries.

{% highlight swift linenos %}
class Test {
  var hello: [String: Int] = [:]
  
  func test() {
    hello[“one”] = 1
    hello[“one”] // 1
  }
}
{% endhighlight %}

## What are hash tables?
Hash tables are data structures or ‘associative arrays’ that work by storing and retrieving data using key-value mapping. Hash tables use 
the key from the hash element to compute a value which is the index where you store the value.

### Hash Function

The computation of the hash element key is commonly called a hash function. A good definition of a hash function is:

A hash function is any function that can be used to map a data set of an arbitrary size to a data set of a fixed size, which falls into 
the hash table. The values returned by a hash function are called hash values, hash codes, hash sums, or simply hashes. There are many 
different ways that you can implement a hash function. After calculating the index, you can insert that hash element into the bucket that 
corresponds to that index.

[Ray Wenderlich Algorithm Club](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Hash%20Table)
A hash table allows you to store and retrieve objects by a “key”.
A hash table is used to implement structures, such as a dictionary, a map, and an associative array. These structures can be implemented by a tree or a plain array, but it is efficient to use a hash table.
This should explain why Swift’s built-in Dictionary type requires that keys conform to the Hashable protocol: internally it uses a hash table, like the one you will learn about here.
