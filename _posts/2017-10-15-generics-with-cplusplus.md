---
layout: post
title: "Generics With C++"
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---


![](https://cdn-images-1.medium.com/max/549/1*cZaRcQPvTZ-BMCLUMkeeBA.jpeg)<figcaption>Special case data type: fox-star</figcaption>

#### Brief Overview

One of the most useful features in Swift is the language support for generics. Recently I’ve been programming with C++ so I want to explore the concepts around generic classes in the language. Generics in C++ can be implemented for classes or functionality. In this example I’m going to stick with C++ class templates, but the ideas behind both of them and the way you go about implementing them are almost identical. Also, I’m not going to dive deeply into template specialization, but I want to note, you can be more specific in how you want to handle a particular type of data, much like the **_where_** specifier in&nbsp;Swift.

At the most basic conceptual level, generics are a way to make code flexible enough to accommodate a variety of data input types. Generics abstract the concepts of data types to their most basic form. [raywenderlich.com](https://www.raywenderlich.com/154371/swift-generics-tutorial-getting-started) has one of the more elegant definitions for generic programming that I’ve come&nbsp;across:

> **Generic programming** is a way to write functions and data types while making minimal assumptions about the type of data being&nbsp;used.
### Getting Started

Let’s break it down in a more concrete way. Say you want to iterate over an array of numbers and perform a function with each element like&nbsp;print:

{% highlight c++ %}

int array [2] = { 16, 2};
int count = sizeof(array)/sizeof(array[0]);

for (int i = 0; i < count; ++i) {
    std::cout << "\n" << array[i] << "\n";
};

{% endhighlight %}

Now imagine if your program needs to do this over and over for many different arrays at various points throughout your application. Writing five lines each time would quickly become tedious. It could also degrade the readability of your code. To fix this we could create a helper function like&nbsp;this:

{% highlight c++ %}

void print_array(int param[]) {
    int count = sizeof(param)/sizeof(param[0]);
    std::cout << "Count: " << count << "\n";
    
    
    for (int i = 0; i < count; ++i) {
        std::cout << "\n" << param[i] << "\n";
    };
}

{% endhighlight %}

This a bit more convenient. We can now print all the values in an array and get the item count using one line. Even so, we’re still tying it to specific data type. We can still do&nbsp;better.

#### Keeping DRY

Imagine we had arrays all over our project and the array held different types of data. If we wanted to to iterate for an array that held non-integer data types we would have to copy and paste this code and change the input type. This will quickly become the downfall of any effort to keep your code **DRY** ( **D** on’t **R** epeat **Y** ourself).

### Templates

C++ implements generic programming concepts through templates. Templates give the compiler a framework to generate code for the types it gets implemented with. With a class, C++ will look at your template as well as the type specified when you created the object, and generate that typed class for&nbsp;you.

Think of it as a cookie cutter in the shape of a star. You can have various kinds of cookie dough, like shortbread, or chocolate-shortbread or chocolate chip cookie dough. The cookie cutter doesn’t care about the ingredients, as long as it is&nbsp;dough.

#### Linked Lists

To prove the power of generics, let’s build a doubly linked list. If you’re new to linked lists, they are data types where each element hold a pointer to the element directly before and after it. A linked list will terminate when the pointer becomes&nbsp;NULL.

{% highlight c++ %}

#include <iostream>

using namespace std;

struct node {
    int data;
    
    struct node *next;
    struct node *prev;
};

class List {
    
private:
    
    node *head, *tail;
    
public:
  
    List() {
        head = NULL;
        tail = NULL;
    };
    
    bool isEmpty();
    void display_list_values();
    void append(int value);
};

void List::append(int value) {
    
    node *new_node = new node;
    new_node->data = value;
    
    if(head==NULL) {
        // If head is null then list doesn't have any nodes, set head and tail to new node value.
        head=new_node;
        tail=new_node;
        new_node=NULL;
    } else {
        // else set current tail to be previous node on chain before new_node then set current tail next value to new_node
        // finally we set tail to be new node value and discard new_node by setting null.
        new_node->prev = tail;
        tail->next = new_node;
        tail = new_node;
        new_node = NULL;
    }
}

bool List::isEmpty() {
    
  // if head is null, there are no items in the list and it is empty so return true.  
    if(head==NULL) {
        return true;
    } else {
   // if head is not null, there are items in the list and it is not empty so return false.  
        return false;
    }
};

void List::display_list_values() {
    
    node *display_node = new node;
    display_node = head;
    
    while(display_node!=NULL) {
        std::cout<<display_node->data<<"\t";
        display_node=display_node->next;
    }
};

{% endhighlight %}

If you were using several linked lists with with different data types this could quickly degenerate into messy spaghetti code.

#### Let’s Be&nbsp;Generic!

To simplify, let’s change a few aspects of the code to make it compatible with generic data&nbsp;types:

{% highlight c++ %}

#include <iostream>

using namespace std;

template <class T>
struct generic_node {
    T data;
    
    struct generic_node *next;
    struct generic_node *prev;
};

template <class T>
class Linked_List {
    
private:
    
    generic_node<T> *head, *tail;
    
public:
    
    Linked_List<T>() {
        head = NULL;
        tail = NULL;
    };
    
    bool isEmpty();
    void display();
    void append(T value);
};


template <class T>
void Linked_List<T>::display () {
    
    generic_node<T> *display_node=new generic_node<T>;
    display_node=head;
    
    while(display_node!=NULL) {
        std::cout<<display_node->data<<"\t";
        display_node=display_node->next;
    }
}

template <class T>
bool Linked_List<T>::isEmpty() {
    
    // if head is null, there are no items in the list and it is empty so return true.  
    if(head==NULL) {
        return true;
    } else {
   // if head is not null, there are items in the list and it is not empty so return false.  
        return false;
    }
};

template <class T>
void Linked_List<T>::append(T value) {
    
    generic_node<T> *new_node = new generic_node<T>;
    new_node->data = value;
    
    if(head==NULL) {
        // If head is null then list doesn't have any nodes, set head and tail to new node value. 
        head=new_node; 
        tail=new_node;
        new_node=NULL; 
    } else {
        // else set current tail to be previous node on chain before new_node then set current tail next value to new_node
        // finally we set tail to be new node value and discard new_node by setting null. 
        new_node->prev = tail;
        tail->next = new_node;
        tail = new_node;
        new_node = NULL; 
    }
}

{% endhighlight %}

We specify **template**  **\<class T\>** at the top giving with generic type **T**. This **T** could eventually be implemented as **char** type&nbsp;like:

```
Linked_List<char> *generic_linked_list = new Linked_List<char>();
```

Or an **int** &nbsp;type:

```
Linked_List<int> *generic_int_linked_list = new Linked_List<int>();
```

Ultimately, generics are another tool which you can use to make to code more maintainable because they help distill essential functionality and promote readability. At the end of the day, it doesn’t make a huge amount of difference to the compiler whether you copied and pasted functionality all over your code or whether you use generics. Code is meant to be read by&nbsp;people.

* * *
