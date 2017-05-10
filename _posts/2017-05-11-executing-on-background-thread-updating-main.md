---
layout: post
title: Executing On Background Thread And Updating To Main 
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

### Background

As I’ve written here before, network requests can throw a wildcard into the performance of your application. The nature of these problems can vary,
from response time to the amount information a request returns. One way you can mitigate issues is by performing these requests on background
threads and dispatching the UI updates that result from them to the main thread. 

### Dispatch Framework

To help manage threading, Apple provides developers with the Dispatch framework. 

_Dispatch comprises language features, runtime libraries, and system enhancements that provide systemic, comprehensive improvements to the support for concurrent 
code execution on multicore hardware in macOS, iOS, watchOS, and tvOS._

One easy way to use Dispatch is with the DispatchQueue class. Apple describes DispatchQueue as:

_DispatchQueue manages the execution of work items. Each work item submitted to a queue is processed on a pool of threads managed by the system._

In practical terms what this means is it allows developers to decide on which thread their code will execute.  Multithreading is a complicated topic, and for this post,
it is not important to get into detail. 

### Let's get started! 

#### The main thread.

The main thread executes synchronously. Why is this important? Synchronous means that they run one after the other. For development purposes what that means is
that if something is running synchronously, it waits for each operation to finish before moving on to the next one.

#### UIApplication 

One other thing I should mention, UIApplication executes on the main thread. So, if something on your main thread takes a long time to complete, it will hold up your 
entire application. That means, for the most part, you should only use the main thread for processes that you know will finish in a short period, like for instance UI updates.
Running a long and complicated operation on the main thread will destroy your Application's responsiveness. 
