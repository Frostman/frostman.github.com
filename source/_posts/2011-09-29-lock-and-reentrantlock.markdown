---
layout: post
title: "Lock and ReentrantLock"
date: 2011-09-29 09:22
comments: true
categories: [Java, Concurrency, Lock, ReadWriteLock, ReentrantLock, ReentrantReadWriteLock]
excerpt: This article is about locks in Java. Here you can read about their usage, fairness policies and performance.
---

This article is about locks in Java. Here you can read about their usage, fairness policies and performance.

Locks are available since **Java 1.5**.

##Interfaces
First of all, lets see at interfaces.

{% codeblock java.util.concurrent.Lock lang:java http://download.oracle.com/javase/6/docs/api/java/util/concurrent/locks/Lock.html javadoc %}
public interface Lock {
    void lock();
    void lockInterruptibly() throws InterruptedException;
    boolean tryLock();
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    void unlock();
    Condition newCondition();
}
{% endcodeblock %}

As we see, it prodives opportunity of acquiring lock by different ways:

* if we are using `lock()` and the lock is not available the current thread will be suspended until the lock will be released;
* `lockInterruptible()` method acquires the lock until the current thread will be interrupted or lock will be released;
* `tryLock()` method acquires the lock only if it is available at the time of invocation (non-blocking, not waiting for
the lock will be released);
* if we want to acquire the lock interruptibly with the specified waiting timeout we should use `tryLock(...)` method.

There is only one method for unlocking the lock: `unlock()` and it works as it named.

Locks are more flexible and configurable alternative for `synchronized` methods and statements, but we should remeber that
instead of synchronized, **noone** will unlock the lock for us if an exception occures or return statement invokes. So, 
in most cases, the following idiom should be used:

{% codeblock lang:java %}
Lock l = ...;
l.lock();
try {
    // some business logic that needs to be synchronized
} finally {
    l.unlock();
}
{% endcodeblock %}



{% codeblock java.util.concurrent.locks.ReadWriteLock lang:java http://download.oracle.com/javase/6/docs/api/java/util/concurrent/locks/ReadWriteLock.html javadoc %}
public interface ReadWriteLock {
    Lock readLock();
    Lock writeLock();
}
{% endcodeblock %}

ReadWriteLock is a pair of assotiated locks. First lock is used **only** for **read-only** operations (supports **multiple**
locks at the same time) and the second lock is for **write** operations (**exclusive** lock). The read lock can be acquired 
only if the write lock is released. When we lock the write lock, it will wait for releasing of all read locks and then acquires.
ReadWriteLock will improve performance in case of using multiple readers and much smaller number of writers, because many of
readers will work at the same time.

##Implementations
Java provides two implementations of these locks that we care about - `ReentrantLock` and `ReentrantReadWriteLock`.
As you see, both of them are reentrant. It means that a thread can acquire the same lock multiple times without any
issue. In fact reentrant locking increments special thread-personal counter (unlocking - decrements) and the lock will
be released only when counter reaches zero.

Each of lock's implementations has additional methods that help us. Here are some of them:

* for `ReentrantLock` class:
  * `isHeldByCurrentThread()` returns true iff the lock is locked by the current thread;
  * `getHoldCount()` returns number (int) of locks on the lock by the current thread;
  * `getQueueLength()` returns an **estimate** number of threads that waits to acquire the lock;
  * `isLocked()` returns true if any thread holds this lock and false otherwise;
* for `ReentrantReadWriteLock` class:
  * `isWriteLocked()` returns true if someone holds the write lock;
  * `isWriteLockedByCurrentThread()` returns true if the lock is locked by the current thread;
  * `getReadHoldCount()` returns number (int) of locks on the read lock by the current thread.

##Fairness
One of the interesting features of reentrant locks is **fairness**. When we are creating an instance of ReentrantLock or 
ReentrantReadWriteLock we can pass fair flag to the constructor. The difference of fair and non-fair locks is in granting
access policy to threads that wait in the queue to lock the lock. The fair lock grants access to the longest-waiting thread, 
so it look like FIFO (First-In-First-Out). A non-fair lock does not guarantee any particular access order.
Performance of fair locks is near to `synchronized` block and non-fair locks is much faster than them.

By default, all locks are non-fair. Synchronized keyword and statement are non-fair, too. Fair locks have some disadvantages:

* performance degradation;
* fair locks do **not guarantiee** fair thread ordering in real world.

Micro-benchmarking in Java is not so good as we want. There are several things that can change results of tests:

* JVM warmup (the code becomes faster and faster while its working);
* Class loading (all application classes must be loaded);
* JIT compilation (JVM needs time to find hot parts of the code);
* GC (gc can happen while benchmarking and increase the time much).

Instead of this, I think that we can do some micro-benchmarks to check fair locks performance. 
We have T threads, each of them will lock the lock, increment global counter, increment personal counter and unlock the lock.
Test will be ended when the global counter reaches N. So, all threads should have equal values of personal counters
(small deviation can be ignored) with fair lock and different values with non-fair lock. Lets see at average results of running
such micro-benchmark. I ran them for 50 times with `N=1000000` and `T=10` on my laptop (`i3 330m 2.13GHz`, `8Gb RAM`, `JDK 1.7.0`) 
and aggregated results. 'Deviation' - this is how personal counter differs from the expected value (`N/T`).

{% codeblock Fair Lock %}
Deviation:
	max: 0,3222%
	avg: 0,0644%
	min: 0,0351%

Average takes: 10304ms
{% endcodeblock %}

{% codeblock Non-fair Lock %}
Deviation:
	max: 20,1312%
	avg: 2,1682%
	min: 0,6022%

Average takes: 104ms
{% endcodeblock %}

You can find sources of the test [here](https://gist.github.com/1250500).
The results of test match javadoc's warning about fair locks absolutly: 

{% codeblock %}
* Programs using fair locks accessed by many threads
* may display lower overall throughput (i.e., are slower; often much
* slower) than those using the default setting, but have smaller
* variances in times to obtain locks and guarantee lack of
* starvation.
{% endcodeblock %}

As we see "much slower" is up to **100** times slower! 

So, we should remember about all features of locks and their configurations.
I think that we can use ReentrantLock and ReentrantReadWriteLock without any indeterminacy now.
