---
layout: post
title: Complexity and Big-O Notation 
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

# Complexity and Big O 

![Complexity graph](https://i.stack.imgur.com/Aq09a.png)

### Introduction 

In this post I will touch on complexity and big O notation. Complexity in terms of programming doesn't necessarily mean one time. It could be time complexity - which is
number of operations in regards to input or space complexity, which is the number of places used for storage in a given operation in regards to it's input. For simplicities sake,
in this post I will be referring to time complexity.  

### Worst case scenario 

When referring to worst case scenario with complexity, we are referring to an input that causes maximum complexity. The difference between a best/worst case scenario for 
complexity may be significant. The difference between best case scenario and worst scenario for searching a hash table can go from constant O(1) time to linear O(n) time. 

### O(1)

An O(1) operation’s complexity is constant regardless of the number of inputs. Accessing the first element in an array will always be O(1). It is the same for an array
with 1000 elements as it is for an array with 10.  In fact accessing any element in your array by it’s index should be a constant complexity operation regardless of whether 
it is the first element or the 1000th. 

### O(log n) 

When dealing with an O(log n) complexity operation, each cycle should reduce the complexity in relation to input. A prime example of this is a binary search. Each iteration
halves the interval within which it searches. 

### O(n)
An O(n) operation’s complexity scales linearly with the number of inputs. If you iterate through all the elements in an array, it is O(n), because the the operation
is dependent on the number of elements in your array. Going through 10 elements in an array requires 10 cycles. If you multiply the array count by 10, the cycles increase to 
100 or 10 times times as much as 10 elements. 

### O(n^2)

An O(n^2) operation’s complexity scales exponentially with the number of inputs. A simple example of an O(n^2) is an operation with a loop within a loop. If you took an
array with 6 elements and for each element in the array accessed nth element in the range of 0..<array.count you would access your array 36 times.  Your complexity is 
not scaling directly with input, but for input squared. A worst case scenario for a bubble sort is O(n^2) 

