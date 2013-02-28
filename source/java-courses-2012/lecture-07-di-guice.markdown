---                                                                                                                     
layout: page                                                                                                            
title: JC12 - Lecture 06. WEB #1: HTTP, HTML, CSS, Servlet API

comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Course 2012 ([back](index.html))
## Lecture 07. DI, IoC, Guice

## План / тезисы

* constructors (`new`);
* factories;
* DI (Dependency Injection) / IoC (Inverse of Control);
* Google Guice.

## Полезная информация

На официальном сайте Google Guice в [wiki](http://code.google.com/p/google-guice/wiki/Motivation?tm=6) находится множество статей про то как пользоваться им, то как он устроен внутри и тд. 

## Практические задания (сделать на занятии)

### Задание #1

Написать `LazyProvider<T>`, который принимает `Provider<T>` в качестве аргумента в конструкторе и вызывает у него `get()` всего 1 раз при первом обращении.
Реализация должна быть потоко-безопасной.

### Задание #2

При помощи `guice-servlet` реализовать следующее приложение:

* фильтр должен писать по `/hello` в респонз "Hello from filter";
* сервлет должен писать по `/hello` в респонз "Hello from Servlet";
* т.о. мы должны увидеть страницу с двумя приветствиями при обращении к `/hello`.
 
## Домашнее задание

### Задание #1

Переделать предыдущее домашнее задание максимально используя Guice.

Например, помимо сервлетов и фильтров, у вас должны быть классы отвечающие за получение логинов-паролей пользователей и проверку способности пользователя просматривать директорию. Эти классы так же должны инжектиться с помощью Guice.

### Задание #2

По [ссылке](http://code.google.com/p/google-guice/wiki/CustomInjections) описано как сделать инжектинг логгера log4j. Необходимо разобраться в работе этого механизма и по аналогии написать инжектинг для slf4j логгера.


