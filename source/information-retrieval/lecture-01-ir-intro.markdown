---                                                                                                                     
layout: page                                                                                                            
title:                                                                                   
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse                                                                                                       
footer: false                                                                                                           
---                                                                                                                     
## IR 2013 ([back](index.html))                                            
## Lecture 01. Information Retrieval Intro  

Основной источник `Маннинг К.Д., Рагхаван П., Шютце Х. - Введение в информационный поиск - 2011`.                                                                                      

## Практическое задание (на занятии) #1 / Домашнее задание

Задание состоит в реализации простейшей системы, поддерживающей следующие команды:

1. `index "path/to/file.txt"` - проиндексировать указанный файл;
2. `search "query"` - искать `query` в проиндексированных файл, дожно выводить названия файлов, подходящих под запрос;
3. `save path/to/store` - сохранить накопленный индекс в файл в произвольном формате;
4. `load path/to/store` - загрузить индексы из указанного файла.

Все эти команды должны поддерживаться на `stdin` (`System.in`) и писать в `stdout` (`System.out`).

Задача будет со временем усложняться, поэтому стоит писать в ООП стиле.

Пример интерфейса, который отвечает за разбиение текста на токены (лексемы):

```java
interface Tokenizer {
	String nextToken();
	boolean hasNextToken();
}
```

На первое время весь индекс можно хранить в виде `Map<String, SortedSet<String>>`: токен -> упорядоченное множество идентификаторов файлов (документов).

Интерфейс, отвечающий за осуществествление поиска в индексе:

```java
interface SearchProcessor {
	Set<String> search(String query, Map<String, SortedSet<String>> index);
}
```