---
layout: post
title: Making A Hash Data Structure In Swift
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

![Hash Table](https://cdn-images-1.medium.com/max/1600/1*ZzVm_vJS1nIl9oB7NDR3Ug.png)

# Adventures In Data Structures
---
## Overview
This post will continue my series on data structures and will touch on the concepts behind and implementation of hash tables. Hash tables
are one of the most commonly used data structures in development. In fact, even if you haven’t heard the term before, you have probably
used them in one form or another.

### O(1) Complexity
One of the reasons you find hash tables used so often is that they are very efficient. The time complexities for a hash table search, item 
insertion and item deletion are on average O(1). This means the time operation time stays constant regardless of the size of the input. As 
you can imagine, this can come in quite handy with large or expanding data sets.

### Key Value Pairing
Key-value pairing is found throughout Swift and Apple’s frameworks/libraries.

*Apple Docs*:
<blockquote>
<p>A key-value pair is a combination of a key and a value.</p>
</blockquote>

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

![Hash Table](https://cdn-images-1.medium.com/max/1600/1*6l31RV3i4xl2eo6WisGP0g.png)

# What are hash tables?
---
Hash tables are data structures or ‘associative arrays’ that work by storing and retrieving data using key-value mapping. Hash tables use 
the key from the hash element to compute a value which is the index where you store the value.

### Hash Function

The computation of the hash element key is commonly called a hash function. A good definition of a hash function is:

A hash function is any function that can be used to map a data set of an arbitrary size to a data set of a fixed size, which falls into 
the hash table. The values returned by a hash function are called hash values, hash codes, hash sums, or simply hashes. There are many 
different ways that you can implement a hash function. After calculating the index, you can insert that hash element into the bucket that 
corresponds to that index.

[Ray Wenderlich Algorithm Club](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Hash%20Table)

<blockquote>
<p> A hash table allows you to store and retrieve objects by a “key”.A hash table is used to implement structures, such as a dictionary, a map, and an associative array. These structures can be implemented by a tree or a plain array, but it is efficient to use a hash table.
This should explain why Swift’s built-in Dictionary type requires that keys conform to the Hashable protocol: internally it uses a hash table, like the one you will learn about here.</p>
</blockquote>

### Hashable Protocol

In Swift you may have seen a protocol called Hashable. Anything that conforms to Hashable needs to have a computed integer property called hashValue. How that property is implemented, determines whether will work as your hash function.

[Apple Docs for Hashable Protocol](https://developer.apple.com/reference/swift/hashable)

<blockquote>
<p> You can use any type that conforms to the Hashable protocol in a set or as a dictionary key. Many types in the standard library conform to Hashable: strings, integers, floating-point and Boolean values, and even sets provide a hash value by default. Your own custom types can be hashable as well. When you define an enumeration without associated values, it gains Hashable conformance automatically, and you can add Hashable conformance to your other custom types by adding a single hashValue property.
A hash value, provided by a type’s hashValue property, is an integer that is the same for any two instances that compare equally. That is, for two instances a and b of the same type, if a == b then a.hashValue == b.hashValue. The reverse is not true: Two instances with equal hash values are not necessarily equal to each other. </p>
</blockquote>


{% highlight swift linenos %}

class Element: Hashable {
    
    var value: Int
    
    var hashValue: Int {
        return value * 10
    }
    
    init(value: Int) {
        self.value = value
    }
}

extension Element: Equatable {
    static func ==(lhs: Element, rhs: Element) -> Bool {
        return lhs.hashValue == rhs.hashValue
    }
}

{% endhighlight %}

### Buckets
Buckets are the index slots in which our hash elements are placed. A bucket corresponds to a specific index.

# Collisions
---
Collisions happen when your hashing function creates duplicate indexes for different keys. In an ideal world, every element in a hash table would calculate a unique index. Unfortunately, most hashing algorithms will return a non-unique index from time to time.
The better the hashing algorithm, the less often this happens. At the same time, the point of using a hash-table is that its performance, if you create an incredibly complex algorithm for calculating the index, this can cut down on that. There is a middle ground in using relatively good hashing function along with logic to handle collisions when they happen.

## Open Addressing (Linear Probing)
One way of dealing with collisions is with open addressing. Open addressing resolves a collision by finding the next available slot. This solution can quickly run up the time complexity and turn operations that should be O(1) — constant to O(n) linear time — meaning the time scales directly with the number of inputs. For this article I won’t go further into depth on Open Addressing.

## Separate chaining

![Separate Chaining](https://cdn-images-1.medium.com/max/1600/1*ItGUOu_Oun1vOF_ro_kCcA.png)

One way to solve collisions is to create buckets that have some sort list structure which elements that compute to that bucket’s index are appended to. For this post, I will use that method.

# Getting Started
---
Finally, let’s write some code! Our hash table will be implemented separate chaining to account for any collisions.

## Creating Our Hash Element

To get started let’s create our hash element. To do this, let’s make a class with the generic parameter’s T and U. T will be the key and U is our value. Because our key needs to be Hashable, we should specify that T needs to conform to the Hashable protocol.

{% highlight swift linenos %}

class HashElement<T: Hashable, U> {
    var key: T
    var value: U?
    
    init(key: T, value: U?) {
        self.key = key
        self.value = value
    }
}

{% endhighlight %}

## Setting Up The Hash Table

When we create our hash table, we should give it a capacity.

{% highlight swift linenos %}

struct HashTable<Key: Hashable, Value> {
    
    typealias Bucket = [HashElement<Key, Value>]
    
    var buckets: [Bucket]
    
    init(capacity: Int) {
        assert(capacity > 0)
        buckets = Array<Bucket>(repeatElement([], count: capacity))
    }
}

{% endhighlight %}

You might have noticed the typealias Bucket. Bucket specifies that a data structures that is an array of HashElement items. Our buckets variable is a two-dimensional array containing arrays of HashElements. In our initialization, we assert that our given capacity is greater than zero.

## Computing Our Index

Finally, let’s create a function that will compute our index:

{% highlight swift linenos %}

struct HashTable<Key: Hashable, Value> {
  // Hash table setup 
  func index(for key: Key) -> Int {
        var divisor: Int = 0
        for key in String(describing: key).unicodeScalars {
            divisor += abs(Int(key.value.hashValue))
        }
        return abs(divisor) % buckets.count
    }
}

{% endhighlight %}

Using [unicodeScalars](https://developer.apple.com/reference/swift/string.unicodescalarview) gives a consistent value to compute our hash function with. Once we’ve computed our divisor value we use the modulo function for the buckets count and return that value.

## Retrieving Data From Table

Now we need need to create a way to retrieve value using our key. Let’s create method value(for key: Key) that return an optional value. The return type should be optional to account for cases where there are is no value associated with a given key.

{% highlight swift linenos %}

struct HashTable<Key: Hashable, Value> {
    
    typealias Bucket = [HashElement<Key, Value>]
    
    var buckets: [Bucket]
    
    init(capacity: Int) {
        assert(capacity > 0)
        buckets = Array<Bucket>(repeatElement([], count: capacity))
    }
    
     func index(for key: Key) -> Int {
        var divisor: Int = 0
        for key in String(describing: key).unicodeScalars {
            divisor += abs(Int(key.value.hashValue))
        }
        return abs(divisor) % buckets.count
    }
    
    func value(for key: Key) -> Value? {
        let index = self.index(for: key)
        
        for element in buckets[index] {
            if element.key == key {
                return element.value
            }
        }
        return nil
    }
}

{% endhighlight %}

# Wrap-Up
---
This concludes the first part. In part two we will finish implementing our hash table and go over the costs and benefits of using it.
