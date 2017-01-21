---
layout: post
title: Simple Principles To Code By
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---
![oops](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/515.gif)


## Hello

Recently, I’ve been working on the overall architecture of my application, [TaskHero](https://github.com/chriswebb09/taskhero). So how's that been working out for me... I'm going to be polite here and leave it at: It's been a great learning experience.

<br>


## It begins...

When I initially built it, my goal was to get it into the App Store as quickly as possible. 


##### Me in October:

![satisfied-seal](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/satisfied-seal-2.jpg)
<br>

## Three months later 


Fast forward a few months later, and the file count has ballooned to 72. So now I want to make updates, make it fancy and whatnot. Not so easy to do when it’s 72 files and changing my code in one part of the application ends up breaking a different piece. 

**Bottom line**: October Me is not nearly as clever as he thinks he is. 


###### Me in January:

 
![computer-guy](https://raw.githubusercontent.com/chriswebb09/chriswebb09.github.io/master/public/Computer-Guy-2.png)

<br>

## Simple principles to code by, not simple to clean up after.


How did we get here: 

> Make it work. Make it right. Make it nice. 


*Some guy named Joe*

First off, I want to define my interpretation of what each of these should mean.

##### Making it work:
This is the stage where you concentrate on the functionality of the application. Things like when the user clicks a button, 
they should get logged in go into this stage. This is the fun part of the cycle.

##### Making it right:
This is a stage where you concentrate on the overall architecture. How does the functionality fit into the overall application? 
How do they interact with each other? If I change something over here, will it break everything?  Can I swap this function out for a better implementation, or have I handicapped myself by tethering it too closely to the functionality of another class? 

*Hint: This is the time to adult and plan ahead. 

##### Make it nice: 
Alright, if we at are this stage, you have a coherently designed and properly functioning application, we're in good shape. You may now lavish attention on the small details and be fancy. If you do this right, you should be able to look at your code and think: "Hmm, that looks fancy." If you're lucky, someone else might look at it and say "Hmm, that looks fancy." 

## Moving Right Along
I think it is important to examine my the first dilemma, the number of files. It’s an atrocity, a crime against code. This application is far simpler than 72 files. The number alone is a neon sign that there is something terribly awry here. If for some reason it did require 72 files, should because the code is so modularly perfect that I thought every function should have the honor of its very own .swift file. I’m not sure what my code could possibly have done to deserve this lavish treatment but whatever. 

<br>

## Questions Need Answers


I still haven’t answered the fundamental issue, how did I get here. It’s simple: I was focusing on making it work far too much and neglecting the making it right part. But what about making it nice? Making it nice isn’t nearly as important as making it right. Making it right can exist without making it nice, making it nice existence depends entirely on the application being built right. 

**Bottom line**: Delaying the satifisfaction of having a finished product and taking the time to think about the future of your project is like the developement equivilent of buying Google stock at it's initial offering price, you will be richly rewarded for your investment. 

That's all I have for now, but this is something I've been thinking about a lot lately so I'm pretty sure there will be a follow up. 
