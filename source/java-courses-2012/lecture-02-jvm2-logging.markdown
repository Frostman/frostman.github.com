---                                                                                                                     
layout: page                                                                                                            
title: JC12 - Lecture 01. JVM bytecode and instrumentation #2 and logging                                                                                                                 
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Сourse 2012 ([back](index.html))
## Lecture 02. JVM bytecode and instrumentation #2 and logging

## Разбор домашнего задания / домашнее задание

Необходимо доделать домашнее задание на реализацию `@Cache` из первой лекции 
[JVM bytecode and instrumentation #1](lecture-01-jvm.html).

Так же необходимо соблюсти следующие требования:

* не использовать reflection;
* аннотация `@Cache` должна работать для методов ничего не возвращающих (void), возвращающих null,
а так же для методов с null аргументами;
* если аннотированный метод выбрасывает exception во время исполнения, то он должен быть
отловлен и закеширован (и выбрасываться при последующих обращениях);
* необходимо логировать следующие события при помощи slf4j-log4j`:
	* метод <method signature> инструментирован (INFO level);
	* инструментированный метод вызван, значение взято из кеша (DEBUG level);
	* инструментированный метод вызван, значение посчитано (DEBUG level);
	* инструментированный метод вызван, значение есть в кеше, но истекло его время жизни (DEBUG level);
* в проекте должен быть log4j.properties файл с настройками log4j.

## Логирование в Java

* зачем нужно логирование;
* чем пользоваться;
* как пользоваться.

### Log4J

На данный момент это самый популярный фреймворк для логирования на платформе Java.
Log4J обладает очень простым API:

```java
package org.apache.log4j;

  public class Logger {

    // Creation & retrieval methods:
    public static Logger getRootLogger();
    public static Logger getLogger(String name);

    // printing methods:
    public void trace(Object message);
    public void debug(Object message);
    public void info(Object message);
    public void warn(Object message);
    public void error(Object message);
    public void fatal(Object message);

    // generic printing method:
    public void log(Level l, Object message);
}
```

Каждый логгер имеет собственное имя, которое может иметь форму "package.of.my.class.MyClass",
тогда настройки логирования определенные для пакета, в который вложен логгер, будут унаследованны.
Так же для каждого логгера задается собственный уровень логирования.

Уровни логирования в порядке возрастания "важности": 

* `TRACE`; 
* `DEBUG`;
* `INFO`;
* `WARN`;
* `ERROR`;
* `FATAL`.

Пример использования логгеров:

```java
   // get a logger instance named "com.foo"
   Logger  logger = Logger.getLogger("com.foo");

   // Now set its level. Normally you do not need to set the
   // level of a logger programmatically. This is usually done
   // in configuration files.
   logger.setLevel(Level.INFO);

   Logger barlogger = Logger.getLogger("com.foo.Bar");

   // This request is enabled, because WARN >= INFO.
   logger.warn("Low fuel level.");

   // This request is disabled, because DEBUG < INFO.
   logger.debug("Starting search for nearest gas station.");

   // The logger instance barlogger, named "com.foo.Bar",
   // will inherit its level from the logger named
   // "com.foo" Thus, the following request is enabled
   // because INFO >= INFO.
   barlogger.info("Located nearest gas station.");

   // This request is disabled, because DEBUG < INFO.
   barlogger.debug("Exiting gas station search");
```

Примеры конфигураций log4j (log4j.properties):

```
# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=DEBUG, A1

# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%-4r [%t] %-5p %c %x - %m%n

# Print only messages of level WARN or above in the package com.foo.
log4j.logger.com.foo=WARN
```

Конфигурация с несколькими аппендерами:

```
log4j.rootLogger=debug, stdout, R

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout

# Pattern to output the caller's file name and line number.
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] (%F:%L) - %m%n

log4j.appender.R=org.apache.log4j.RollingFileAppender
log4j.appender.R.File=example.log

log4j.appender.R.MaxFileSize=100KB
# Keep one backup file
log4j.appender.R.MaxBackupIndex=1

log4j.appender.R.layout=org.apache.log4j.PatternLayout
log4j.appender.R.layout.ConversionPattern=%p %t %c - %m%n
```

Для выводы отладочной информации предпочтительнее сначала проверять уровень логгирования
используемого логгера, т.е. вместо

```java
logger.debug("Entry number: " + i + " is " + String.valueOf(entry[i]));
```

лучше писать

```java
if(logger.isDebugEnabled() {
    logger.debug("Entry number: " + i + " is " + String.valueOf(entry[i]));
}
```

Maven dependency:

```java
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```


### SLF4J (Simple Logging Facade 4 Java)

API Log4J слишком простой, иногда необходимы дополнительные возможности, а так же не хочется
быть привязанным к конкретному логгеру.

SLF4J не реализует логирование, а только предоставляет API для этого и средства, позволяющие
использовать различные логгеры через одно общее API.

Получение логгера:

```java
Logger logger = LoggerFactory.getLogger(HelloWorld.class);
logger.info("Hello World");
```

Если подключить только slf4j (`slf4j-api-1.7.1.jar`) к проекту, 
то в `sout` будет выведена ошибка следующего вида:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Чтобы slf4j заработал необходимо подключить bridge для конкретного логгера и при необходимости
сам логгер.

Например, для использования стандатного логгера Java, необходимо подключить еще и `simple` bridge: `slf4j-simple-1.7.1.jar`.

Slf4J предоставляет огромное число дополнительных возможностей, таких как маркеры и шаблоны:

```java
Logger logger = LoggerFactory.getLogger(Main.class);

public void setTemperature(Integer temperature) {
	oldT = t;        
    t = temperature;
    
    logger.debug("Temperature set to {}. Old temperature was {}.", t, oldT);

    if(temperature.intValue() > 50) {
      logger.info("Temperature has risen above 50 degrees.");
    }
}
```

Подключив одну из следующих зависимостей можно использовать конкретный логгер:

1. slf4j-log4j12-1.7.1.jar (log4j);
2. slf4j-jdk14-1.7.1.jar (java.util.logging);
3. slf4j-nop-1.7.1.jar (no operations);
etc.

Maven:

```
<dependency> 
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>1.7.1</version>
</dependency>
```

```
<dependency> 
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-jdk14</artifactId>
  <version>1.7.1</version>
</dependency>
```