---
layout: post
title: "Python Multiprocessing"
date: 2016-08-31 21:21
comments: true
categories: [Python, Engineering]
---
It has been a long time since I updated my last blog. I decided to continue to write blogs to summarize my technical work.

In the last six months, Python has been my main programming language. I implemented a crawler and several web-based applications. I tried many excellent Python frameworks such as Django, Bottle, Scrapy. I will write several blogs to summarize the knowledge I got in my work.

The topic of today is multi-processing.

### Process vs Thread
Normally we prefer thread to process because in most systems threads are cheaper than processes.  And threads share the same virtual memory space which they have independent calling stack. But because of global interpreter lock, only one thread can execute in a particular time. To side-step this drawback and utilize the advantage of multi-cores, I prefer processes to threads in Python.

### Multiprocessing Pool
In the multiprocessing module of python, process pool is a very simple way to distribute work to many workers, so it became my first choice in the very beginning. But I met many problems while using it.

#### memory leak
As described in the [*post1*](http://stackoverflow.com/questions/21485319/high-memory-usage-using-python-multiprocessing/21613370) [*post2*](http://stackoverflow.com/questions/8777324/leaking-memory-with-multiprocessing-pool-even-after-close), the memory usage will increase after a while. So for a server side application which runs for a long time, multiprocessing pool is not a good choice.

#### pickling error
As described in the [*post*](http://stackoverflow.com/questions/8804830/python-multiprocessing-pickling-error?rq=1), I had to use a global wrapper for the method to be invoked in pool which made my code kind of ugly.

Using multiprocess pool, you lose a lot of control over your working processes which is not so convenient for complicated tasks. With all these drawbacks, I switched to Process class. I implemented a 2-level producer-consumer pattern which worked quite well.

### Producer-consumer
In this [*article*](https://pymotw.com/2/multiprocessing/communication.html), there is a good example of producer-consumer pattern which uses JoinableQueue and Poison Pill trick.

#### JoinableQueue vs Queue

JoinableQueue is a subclass of Queue and it adds task_done() and join() methods. [*ref*](http://stackoverflow.com/questions/31230241/queue-vs-joinablequeue-in-python)

