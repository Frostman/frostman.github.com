---                                                                                                                     
layout: page                                                                                                            
title: JC12 - Lecture 01. JVM bytecode and instrumentation                                                                                                                 
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Сourse 2012 ([back](index.html))
## Lecture 01. JVM bytecode and instrumentation

## Теория

<div class="prezi-player"><style type="text/css" media="screen">.prezi-player { width: 1024px; } .prezi-player-links { text-align: center; }</style><object id="prezi_sp97yqllrb4y" name="prezi_sp97yqllrb4y" classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" width="1024" height="768"><param name="movie" value="http://prezi.com/bin/preziloader.swf"/><param name="allowfullscreen" value="true"/><param name="allowFullScreenInteractive" value="true"/><param name="allowscriptaccess" value="always"/><param name="bgcolor" value="#ffffff"/><param name="flashvars" value="prezi_id=sp97yqllrb4y&amp;lock_to_path=1&amp;color=ffffff&amp;autoplay=no&amp;autohide_ctrls=0"/><embed id="preziEmbed_sp97yqllrb4y" name="preziEmbed_sp97yqllrb4y" src="http://prezi.com/bin/preziloader.swf" type="application/x-shockwave-flash" allowfullscreen="true" allowFullScreenInteractive="true" allowscriptaccess="always" width="1024" height="768" bgcolor="#ffffff" flashvars="prezi_id=sp97yqllrb4y&amp;lock_to_path=1&amp;color=ffffff&amp;autoplay=no&amp;autohide_ctrls=0"></embed></object><div class="prezi-player-links"><p>Watch <a title="Java Bytecode" href="http://prezi.com/sp97yqllrb4y/java-bytecode/">Java Bytecode</a> on <a href="http://prezi.com">Prezi</a> or download as <a href="http://f.slukjanov.name/jc12/java-bytecode.pdf">pdf</a></p></div></div>

## Практическое задание (на занятии) #1

1. Убедиться в наличии Oracle JDK 6/7 и JAD, скачать и установить при необходимости.
Убедиться в работе консольных приложений javac, javap и jad. 

2. Попробовать написать простейший "Hello, World!", скомпилировать его и посмотреть его байткод при помощи javap
и на его декомпилированный код при помощи jad. Убедиться в понимании каждой инстрикции.

Повторить исследование на следующем коде:

```java
public class Example {
    public static void main(String[] args) {
        Example example = new Example(123);
        example.printNumber();
        System.out.println(example.getIncNumber());
    }

    public final int number;

    public Example(int number) {
        this.number = number;
    }

    public void printNumber() {
        System.out.println(number);
    }

    public int getIncNumber() {
        return number + 1;
    }
}
```

<div class="prezi-player"><style type="text/css" media="screen">.prezi-player { width: 1024px; } .prezi-player-links { text-align: center; }</style><object id="prezi_gqacanaabins" name="prezi_gqacanaabins" classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" width="1024" height="768"><param name="movie" value="http://prezi.com/bin/preziloader.swf"/><param name="allowfullscreen" value="true"/><param name="allowFullScreenInteractive" value="true"/><param name="allowscriptaccess" value="always"/><param name="bgcolor" value="#ffffff"/><param name="flashvars" value="prezi_id=gqacanaabins&amp;lock_to_path=1&amp;color=ffffff&amp;autoplay=no&amp;autohide_ctrls=0"/><embed id="preziEmbed_gqacanaabins" name="preziEmbed_gqacanaabins" src="http://prezi.com/bin/preziloader.swf" type="application/x-shockwave-flash" allowfullscreen="true" allowFullScreenInteractive="true" allowscriptaccess="always" width="1024" height="768" bgcolor="#ffffff" flashvars="prezi_id=gqacanaabins&amp;lock_to_path=1&amp;color=ffffff&amp;autoplay=no&amp;autohide_ctrls=0"></embed></object><div class="prezi-player-links"><p>Watch <a title="Java Agents" href="http://prezi.com/gqacanaabins/java-agents/">Java Agents</a> on <a href="http://prezi.com">Prezi</a> or download as <a href="http://f.slukjanov.name/jc12/java-agents.pdf">pdf</a></p></div></div>

Репозиторий с примерами: [github.com/Frostman/jc12-01](https://github.com/Frostman/jc12-01)

Javassist tutorial: [http://www.csg.ci.i.u-tokyo.ac.jp/~chiba/javassist/tutorial/tutorial.html](http://www.csg.ci.i.u-tokyo.ac.jp/~chiba/javassist/tutorial/tutorial.html)

## Практическое задание (на занятии) #2 / Домашнее задание

Необходимо написать небольшую библиотеку, позволяющую кешировать вызовы методов.
Она должна предоставлять два способа декларировать кеширование:

1. аннотация `@Cache(key, maxEntriesForKey, ttl)`;
2. helper-метод `CallCache.instrumentMethod(className, methodSignature, key, maxEntriesForKey, ttl)`.

Где:

* `key` - ключ для кеширования (по-умолчанию, строка вида `net.company.Worker#methodName(int, java.lang.String)void`);
* `maxEntriesForKey` - число закешированных результатов для одного ключа (по-умолчанию, -1, т.е. не ограниченно);
* `ttl` - время жизни конкретного закешированного результата в миллисекундах (по-умолчанию, 10 секунд).

Результаты выполнения методов должны храниться по указанному в аннотации ключу плюс хеш-код от всех переданных в метод аргументов.
При превышении числа записей на ключ, самые старые должны автоматически удаляться (LRU).
При превышении времени жизни закешированного результата исполнения метода, он должен быть пересчитан заново и закеширован.
Пока что не нужно задумываться о thread-safety библиотеки.

P.S. jad и javassist можно найти здесь: [f.slukjanov.name/jc12](http://f.slukjanov.name/jc12/)
