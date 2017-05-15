---
layout: post
title: Linked Lists - Data Structures In Real World
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

![Linked List](https://cdn-images-1.medium.com/max/1600/1*iMYmkYDCSrXXdwpbqm-ekA.jpeg)

# Overview
When you first start diving into data structures, a lot of the discussions/reading tend to be abstract or even academic. While it can 
be good to learn these concepts in isolation, adding some real world context can help give a fuller picture of the purpose a data structures can serve.

### My Experience with Applying Data Structures
Recently, when building a music streaming application, I had the opportunity to apply real-world context to a data structure. I was
able to implement a modified double-linked list as the data structure for my playlist.

### What is a Linked List?
A linked list is a sequence data structure, which connects elements, called nodes, through links. Unlike an array data structure, 
a node in a linked list isn’t necessarily positioned close to the previous element or the next element. Nodes in a linked list are connected by pointers. Linked lists are null terminating, which means whenever the pointer to the next node is null, the list has
reached the end.

#### Single Linked List
![Single Linked List](https://cdn-images-1.medium.com/max/1600/1*eVIWTiVDIBcOBGwdo53Mgw.png)
In single linked list, element or nodes only link to the next element in the list. A single linked list is null terminating meaning when the pointer is null, the list is finished.

#### Double Linked List
![Double Linked List](https://cdn-images-1.medium.com/max/1600/1*RRHHkjHW7aWXS3RTGl08Bw.png)
In a double linked list, each node contains a reference to the previous node and the next node (as long as they aren’t the head or tail node.) Each node has a value property.

# Why A Linked List?
The reason I chose a double linked list was that the structure suited the behavior of a music playlist.

![Skip](https://cdn-images-1.medium.com/max/1600/1*Mz4CcX2rZaTJpAdWBqx9sQ.png)
**Skip Back/Forward**
Because each node in a double linked list has a pointer the previous and next node, it is easy to implement skip forward/backward functionality.

![Play next](https://cdn-images-1.medium.com/max/1600/1*ss6V0n9MwHFme-gzee00Wg.png)
**Play Next Track **
The pointer to the next node also makes it quite easy to start the next track when a track is over.

![Append](https://cdn-images-1.medium.com/max/1600/1*Xf7Og9y2kjnObjcg05tILg.jpeg)
**Append**
When you add a new track to a playlist, you tack it on to the end. In a linked list, adding a new element is constant time — O(1) operation.

![Replay](https://cdn-images-1.medium.com/max/1600/1*nMilfFlm4MwPgStuSzabkw.png)
**Beginning/End**
Finally, because a linked list has head and tail properties, this provides for an easy way to delineate the beginning and end of a playlist.

### List Node
Let’s create a basic list node for a doubly-linked list. We can do this by specifying a class called LLNode which has a Generic T. We can use T as our value:

### Linked List
Now let’s create our Linked List class. This class should also specify a generic T which can be the value for nodes.

Our linked list has a private head and tail property as well as a first and last property. Additionally it has a boolean property, isEmpty, and an append method. There are other properties and methods that a linked list can implement, like removeAll, reverse ect. For this example, this should give you a basic idea of the structure.

# Modifying Our Linked List To Make A Playlist

### The PlaylistItem
Let’s see if you can recognize the LLNode inside the PlaylistItem.

As you can see we’ve substituted the Generic T for a optional iTrack properties and made our PlaylistItem conform to the Equatable protocol. Besides that, it is pretty much the same as the LLNode.

### The Playlist
Our playlist structure is very similar to a normal double linked list. There is a head, which is the first item in our playlist, and tail which is the last item.

### Optimizing Item Count
You might noticed that I’ve added an integer property called itemCount. Some implementations of a linked list will use a computed property for the itemCount. The more optimized solution is to update the item count in your code. Computing the count requires traversing your list. This adds a hefty dose of complexity. While it may be more annoying in the short term to update the item count value depending on the operation, there is a performance bonus from doing this.

### Benefits and Drawbacks
There are benefits and drawbacks to the using a LinkedList data structure. One of the biggest drawbacks is that, given there items are not stored in adjacent memory, operations like itemAtIndex will have a O(n) complexity because you will need to traverse your input to get to the specified index.

# Wrap-Up
Hopefully this adds some clarity to using linked lists in real world scenarios. This is just one implementation. The are other ways to apply it as well. Try to see if you can find a different reason to implement a linked list.
