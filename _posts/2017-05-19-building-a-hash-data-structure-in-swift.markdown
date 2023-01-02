---
title: "Building A Hash Data Structure In Swift"
layout: post
date: 2017-05-19 22:48
image: /11w5lxGBI8zAAIgSD1vJAEw.png
headerImage: true
tag:
- iOS
- swift
- data structure
- hash
- hash table
category: blog
author: chriswebb
description: Adventures In Data Structures
---

# Overview

This post will continue my series on data structures and will touch on the concepts behind and implementation of hash tables. Hash tables are one of the most commonly used data structures in development. In fact, even if you haven’t heard the term before, you have probably used them in one form or another.

### O(1) Complexity

One of the reasons you find hash tables used so often is that they are very efficient. The time complexities for a hash table search, item insertion, and item deletion are on average O(1). This means the operation time stays constant regardless of the size of the input. As you can imagine, this can come in quite handy with large or expanding data sets.

### Key Value Pairing

The concept of key-value pairing is found throughout Swift and Apple’s frameworks/libraries.

**Apple Docs:**

A key-value pair is a combination of a key and a value.
One place you come across key-value pairing is when using dictionaries.

{% highlight swift %}

class Test {
  var hello: [String: Int] = [:]

  func test() {
    hello[“one”] = 1
    hello[“one”] // 1
  }
}

{% endhighlight %}

## What are hash tables?

Hash tables are data structures or ‘associative arrays’ that work by storing and retrieving data using key-value mapping. Hash tables use the key from the hash element to compute a value which is the index where you store the value.

### Hashing
You may have heard of hashing or a hash before now. Programmers use the concept all over. From matching data to ensuring security, hashing is an excellent way to keep track of the identity of a piece of data. One use for a hashing is for trying to locate duplicate files. Because a hash algorithm theoretically creates a unique output for a given input, if two datasets are identical they should return the same hash.

### Hash Function
The computation of the hash element key is commonly called a hash function. A good definition of a hash function is:

A hash function is any function that can be used to map a data set of an arbitrary size to a data set of a fixed size, which falls into the hash table. The values returned by a hash function are called hash values, hash codes, hash sums, or simply hashes.
There are many different ways that you can implement a hash function. After calculating the index, you can insert that hash element into the bucket that corresponds to that index.

Ray Wenderlich Algorithm Club

**A hash table allows you to store and retrieve objects by a “key”.**

*A hash table is used to implement structures, such as a dictionary, a map, and an associative array. These structures can be implemented by a tree or a plain array, but it is efficient to use a hash table.
This should explain why Swift’s built-in Dictionary type requires that keys conform to the Hashable protocol: internally it uses a hash table, like the one you will learn about here.*

## Hashable Protocol In Swift

In Swift, you may have seen a protocol called Hashable. Anything that conforms to Hashable needs to have a computed integer property called hashValue. How that property is implemented, determines whether will work as your hash function.

**Apple Docs for Hashable Protocol**

*You can use any type that conforms to the Hashable protocol in a set or as a dictionary key. Many types in the standard library conform to Hashable: strings, integers, floating-point and Boolean values, and even sets provide a hash value by default. Your own custom types can be hashable as well. When you define an enumeration without associated values, it gains Hashableconformance automatically, and you can add Hashable conformance to your other custom types by adding a single hashValue property.*

*A hash value, provided by a type’s hashValue property, is an integer that is the same for any two instances that compare equally. That is, for two instances aand b of the same type, if a == b then a.hashValue == b.hashValue. The reverse is not true: Two instances with equal hash values are not necessarily equal to each other.*

{% highlight swift %}

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

# Buckets

Buckets are the index slots in which our hash elements are placed. A bucket corresponds to a specific index.

*In general, a hashing function may map several different keys to the same index. Therefore, each slot of a hash table is associated with (implicitly or explicitly) a set of records, rather than a single record. For this reason, each slot of a hash table is often called a bucket, and hash values are also called bucket indices.*

Stack Overflow

*In computing, the term bucket can have several meanings. It is used both as a live metaphor, and as a generally accepted technical term in some specialised areas. A bucket is most commonly a type of data buffer or a type of document in which data is divided into regions.*

## Collisions
Collisions happen when your hashing function creates duplicate indexes for different keys. In an ideal world, every element in a hash table would calculate a unique index. Unfortunately, most hashing algorithms will return a non-unique index from time to time.

The better the hashing algorithm, the less often this happens. At the same time, the point of using a hash-table is that its performance, if you create an incredibly complex algorithm for calculating the index, this can cut down on that. There is a middle ground in using relatively good hashing function along with logic to handle collisions when they happen.

## Open Addressing (Linear Probing)

One way of dealing with collisions is with open addressing. Open addressing resolves a collision by finding the next available slot. If it is at the end of the table and cannot find an open bucket, it loops back to the beginning of our table. This solution can quickly run up the time complexity and turn operations that should be O(1) — constant to O(n) linear time — meaning the time scales directly with the number of inputs. For this article I won’t go further into depth on Open Addressing.

## Separate chaining

One way to solve collisions is to create buckets that have some sort list structure which elements that compute to that bucket’s index are appended to. A linked list a great data structure to use here. For this post, I will use chaining.

# Getting Started

Finally, let’s write some code! Our hash table will be implemented separate chaining to account for any collisions.

## Creating Our Hash Element

To get started let’s create our hash element. To do this, let’s make a class with the generic parameter’s T and U. T will be the key and U is our value. Because our key needs to be Hashable, we should specify that T needs to conform to the Hashable protocol.

{% highlight swift %}

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

{% highlight swift %}

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

{% highlight swift %}

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

Using unicodeScalars gives a consistent value to compute our hash function with. Once we’ve computed our divisor value we use the modulo function for the buckets count and return that value.

## Retrieving Data From Table

Now we need need to create a way to retrieve the value using our key. Let’s create method value(for key: Key) that returns an optional value. The return type should be optional to account for cases where there is no value associated with a given key.

{% highlight swift %}

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

## Subscripts

### What is a subscript?

There are still a few things missing from our data structure which we will need to add. For one, we can’t add or update the values. Also, our hash table does not have a subscript implemented that you commonly see in collection types. Adding a subscript to our hash table will give the familiar key, value access that you get with dictionaries.

myDictionary[“key”] = 0
print(myDictionary[“key”]) // 0

The subscript we will implement in our hash table will be analogous with the implementation seen in a standard dictionary. Adding a subscript subscript simplifies the process of storing and retrieving specific key value pairs. It combines the setter and getters for the values into one format instead of requiring two different methods.

Classes, structures, and enumerations can define subscripts, which are shortcuts for accessing the member elements of a collection, list, or sequence. You use subscripts to set and retrieve values by index without needing separate methods for setting and retrieval. For example, you access elements in an Array instance as someArray[index] and elements in a Dictionary instance as someDictionary[key].

You can define multiple subscripts for a single type, and the appropriate subscript overload to use is selected based on the type of index value you pass to the subscript. Subscripts are not limited to a single dimension, and you can define subscripts with multiple input parameters to suit your custom type’s needs.
Apple’s Swift Programming Book

#### Implementing Our Subscript

To start with, I added a model method which takes in a key and returns a string. While at the moment this may seem redundant or unnecessary, you’ll need to translate key data types to a consistent type that we can use as our hash value. Having this method in here now sets you up for later.

Earlier I wrote that the subscript could substitute for retrieval and setter methods. To do this, we’ll need to create those methods and invoke them properly in our custom subscript.

{% highlight swift %}

struct HashTable<Key: Hashable, Value> {

  // Prior Hash Table Implementation

    func model(with element: Key) -> String? {
        switch element {
        case is String:
            return String(describing: element)
        case is Int:
            let stringElement = String(describing: element)
            return stringElement
        default:
            return nil
        }
    }

    subscript(key: Key) -> Value? {
        get {
            return value(for: key)
        }
        set {
            if let value = newValue {
                updateValue(value, forKey: key)
            } else {
                removeValue(for: key)
            }
        }
    }
}

{% endhighlight %}

### Value Operations

In the code below I’ve written the method signatures for functions to set and retrieve values:

mutating func updateValue(_ value: Value, forKey key: Key) -> Value?

mutating func removeValue(for key: Key) -> Value?

Side Note On @discarableResult

If you haven’t seen ‘@discardableResult’ before, all that means is despite the fact that these mutating functions return a value, we may not need to use it so the compile can ignore that value.

{% highlight swift %}

struct HashTable<Key: Hashable, Value> {

  // Code added earlier

    @discardableResult
    mutating func updateValue(_ value: Value, forKey key: Key) -> Value? {
        var itemIndex: Int
        itemIndex = self.index(for: key)
        for (i, element) in buckets[itemIndex].enumerated() {
            if element.key == key {
                let oldValue = element.value
                buckets[itemIndex][i].value = value
                return oldValue
            }
        }
        buckets[itemIndex].append(HashElement(key: key, value: value))
        return nil
    }

    @discardableResult
    mutating func removeValue(for key: Key) -> Value? {
        let index = self.index(for: key)
        for (i, element) in buckets[index].enumerated() {
            if element.key == key {
                buckets[index].remove(at: i)
                return element.value
            }
        }
        return nil
    }
}

{% endhighlight %}

We can now use those two methods as the getter/setters for our subscript. You can now store and retrieve values using yourObjectName[“key”].

The Final Product
Below is the completed hash table data structure. You can also get the gist of the code here.

{% highlight swift %}

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
            print(key.hashValue)
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

    func model(with element: Key) -> String? {
        switch element {
        case is String:
            return String(describing: element)
        case is Int:
            let stringElement = String(describing: element)
            return stringElement
        default:
            return nil
        }
    }

    subscript(key: Key) -> Value? {
        get {
            return value(for: key)
        }
        set {
            if let value = newValue {
                updateValue(value, forKey: key)
            } else {
                removeValue(for: key)
            }
        }
    }

    @discardableResult
    mutating func updateValue(_ value: Value, forKey key: Key) -> Value? {
        var itemIndex: Int
        itemIndex = self.index(for: key)
        for (i, element) in buckets[itemIndex].enumerated() {
            if element.key == key {
                let oldValue = element.value
                buckets[itemIndex][i].value = value
                return oldValue
            }
        }
        buckets[itemIndex].append(HashElement(key: key, value: value))
        return nil
    }

    @discardableResult
    mutating func removeValue(for key: Key) -> Value? {
        let index = self.index(for: key)
        for (i, element) in buckets[index].enumerated() {
            if element.key == key {
                buckets[index].remove(at: i)
                return element.value
            }
        }
        return nil
    }
}
 {% endhighlight %}

Wrap-Up
As one of the most common data structures, you’ll find out in the wild there area wealth of way to use a hash table in your code. The versatility and efficiency of hash tables make them ideal for a wide variety of use cases. The concepts underlying hash tables have been in use for a long time before the digital age. How libraries organize their books is an excellent example of that concept at work without computers.

Good implementations of a hash data structures share some common features:

The hash function is easy to compute

They avoid collisions

Should consistently consistently compute the same value for a given input
When Should You Use A Hash Table?
Hash tables are ideal for databases. Whenever you have a large or growing set of data, you should at least consider whether it would make sense to implement a hash data structure. Because the time complexity is O(1) for storing and retrieving, it scales well as the amount of information grows.

When Should You Not Use A Hash Table?
One place where you would not want to use a hash table is when you have to iterate over a large amount of data.

That’s all for now. Please feel free to leave feedback!
