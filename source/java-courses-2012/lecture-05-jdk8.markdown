---                                                                                                                     
layout: page                                                                                                            
title: JC12 - Lecture 05. JDK 8                                                                                                                
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Course 2012 ([back](index.html))
## Lecture 05. JDK 7-8

### JDK 7

The main new things in JDK 7 are:

* ForkJoin framework;
* new bytecode verification system with type checking;
* syntax sugar - Project Coin:
	* strings in switch;
	* binary integral literals and underscores in numeric literals;
	* multi-catch and more precise rethrow;
	* improved type inference for generic instance creation (`diamond`-operator);
	* try-with-resources statement;
	* simplified varargs method invocation;
* JSR 292: Dynamic langs support:
	* invokedynamic;
	* method handlers;
	* used by Groovy, Jython, JRuby, etc.

### JDK 8

The main new things for us:

* lambdas (closures);
* stream (bulk) operations;
* defenders.

There are much more changes in JDK 8, you can find all of them [here](http://openjdk.java.net/projects/jdk8/features).

*SAM* == single abstract method.

*Functional interface* - interface with only one abstract method.

*lambda* - instance of functional interface.

[repo with code examples](https://github.com/Frostman/jdk8-demo)

### lambdas:

* class `LambdaTest`;
* class `MethodRefTest`;
* class `CaptureTest`;
* class `FibonacciTest`;
* class `ThreadLocalTest`;
* class `WeirdFunctionTest`.

### lambdas lifecycle:

* linkage;
* capture;
* invocation.

### anon/inner classes lifecycle:

* class loading;
* creation (new);
* invocation.

### streams:

* class `StreamApiTest`;
* class `ExternalInternalTest`;
* class `SeqParTest`;
* class `LazyEagerTest`.

### defenders:

* class `DefenderTest`.