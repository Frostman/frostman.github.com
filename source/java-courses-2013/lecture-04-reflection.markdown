---                                                                                                                     
layout: page                                                                                                            
title: JC13 - Lecture 04. Reflection                                                                                                                 
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Сourse 2013 ([back](index.html))
## Lecture 04. Reflection

## Java Class Loaders




## Практическое задание (на занятии) #1 / Домашнее задание

Необходимо реализовать класс `BeanUtil` со следующим функционалом (статическими методами):

* `properties(Object bean)` - список всех property указанного `bean`, например: `["a", "b", "c", "d"]`;
* `get(Object bean, String props)` - получение значения property по имени с поддержкой вложенности, т.е. `get(bean, "a/b/c/d")` должен превратиться в вызов `bean.getA().getB().getC().getD()`;
* `set(Object bean, String props, Object value)` - аналогично `get` выставляет значение, т.е. `set(bean, "a/b/c/d", "new value!")` должен превратиться в вызов `bean.getA().getB().getC().setD("new value!")`
* `type(Object bean, String props)` - аналогично `get`, но возвращает только тип поля, например `type(bean, "a/b/c/d")` вернет `class java.lang.String`.

Некоторые определения:

* `Java Bean` - класс с private полями, как минимум конструктором по-умолчанию и геттерами-сеттерами;
* `property` - это нечто для чего есть геттер и сеттер, т.е. если есть getFoo() и setFoo(), то есть property `foo`.