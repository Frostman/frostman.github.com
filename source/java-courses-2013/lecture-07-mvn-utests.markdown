---                                                                                                                     
layout: page                                                                                                            
title: JC13 - Lecture 07. Maven & Unit tests                                                                                                                 
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Сourse 2013 ([back](index.html))
## Lecture 07. Maven & Unit tests

### Maven

Документация, инструкции по установке - http://maven.apache.org

Основные goals:

* clean - очистка временных файлов 
* compile - компиляция исходных кодов
* test - компиляция исходных кодов и запуск тестов
* package - сборка jar файла со скомпилированными исходниками

Цели можно комбинировать, например - `mvn clean package`.

Для создания чистого мавен проекта можно использовать команду `mvn archetype:generate`.

## Домашнее задание #1

Возьмите свое решение задачи номер 256 с acm.sgu.ru - http://acm.sgu.ru/univer/problem.php?contest=0&problem=256
и оформите его в виде Maven проекта таким образом чтобы у вас был некий метод `convertToMetre(String)`,
который решал бы задачу для одной строки. Так же необходимо написать некоторое количество unit test'ов, которые
полностью покрывали бы все возможные случаи.
