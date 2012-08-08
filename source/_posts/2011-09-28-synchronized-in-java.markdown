---
layout: post
title: "Synchronized in Java"
date: 2011-09-28 21:01
comments: true
categories: [Java, Concurrency, Synchronized]
excerpt: Small article about the usage of "synchronized" keyword and statement.
---

We need synchronizations, when we are writing applications that work with variables from more than one thread. The most simple
way to avoid issues of parallel accessing to variable is to use synchronized statement. In Java there are two ways to use
`synchronized`:

* use statement synchronized with some monitor-object to create critical section (this is also called "synchronized block"): 
{% codeblock %}
synchronized(monitorObject) {
  // Critical section :: 
  // this code block can be accessed 
  // by only one thread at the time
}
{% endcodeblock %}

* mark method with `synchronized` keyword and then:
	* if method is static, it is equal to `synchronized(DeclaredClass.class) { method body }`;
	* if method is not static, it is equal to `synchronized(this) { method body }`.

So, `synchronized` takes care about preventing locks in case of multiple read/write access to variables from more than one thread
In the latest JVM (since 1.5) `synchronized` overhead is very low in case of unused synchronizations (when only one thread
try to enter into the synchronized section at a time), but in the old JVMs `synchronized` was not optimized.

`synchronized` keyword is not so flexible and configurable as we want sometimes, so you can read about Lock and ReadWriteLock in Java
[here](/blog/2011/09/29/lock-and-reentrantlock/) (my next article).
