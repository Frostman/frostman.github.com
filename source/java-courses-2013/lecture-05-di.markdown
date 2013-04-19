---                                                                                                                     
layout: page                                                                                                            
title: JC13 - Lecture 05. DI & Annotations                                                                                                                 
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Сourse 2013 ([back](index.html))
## Lecture 05. DI & Annotations

## Java Class Loaders




## Практическое задание (на занятии) #1 / Домашнее задание

Необходимо реализовать простейший DI (Dependency Injection) фреймворк.

Аннотация `@Inject`:

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface Inject {
    public static final String DEFAULT_NAME = "";

    String value() default DEFAULT_NAME;
}
```

* Поля помеченные `@Inject` должны быть автоматически инициализированны с использованием инжектора;
* если указано имя объекта - `@Inject("test-str")`, то должен грузиться объект, зарегистрированный под именем `"test-str"`.

Методы класса `Injector`:

* `public static <T> T getInstance(Class<T> clazz)` - получить представителя указанного объекта, в котором все поля помеченные аннотацие тоже проинициализированны с использованием этого метода или перегруженного варианта с дополнительным аргументом - именем объекта;                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
* `public static <T> T getInstance(Class<T> clazz, String name)`;
* `public static void bind(Class<T> clazz, T value)` - регистрация конкретного значения для указанного класса (вместо инстанциирования каждый раз);
* `public static void bind(Class<T> clazz, T value, String name)`.

Пример:

```java
public class Sample {
    public static void main(String[] args) {
        Injector.bind(String.class, "str-default-value");
        Injector.bind(String.class, "str-a-value", "str-a");
        B val = Injector.getInstance(B.class);
        System.out.println(val); // [ba=[a='str-a-value', b='str-default-value']]
    }
}

class A {
    @Inject("str-a")
    private String a; // Injector.getInstance(String.class, "str-a")

    @Inject
    private String b; // Injector.getInstance(String.class)

    @Override
    public String toString() {
        return "[a='" + a + "', b='" + b + "']";
    }
}

class B extends A {
    @Inject
    private A ba; // Injector.getInstance(A.class)

    @Override
    public String toString() {
        return "[ba=" + super.toString() + "]";
    }
}
```