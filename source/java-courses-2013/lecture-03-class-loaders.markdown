---                                                                                                                     
layout: page                                                                                                            
title: JC13 - Lecture 03. Class Loaders                                                                                                                 
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Сourse 2013 ([back](index.html))
## Lecture 03. Class Loaders

## Java Class Loaders




## Практическое задание (на занятии) #1 / Домашнее задание

Необходимо реализовать класс `EncryptedClassesClassLoader`.

Класс-ключ: `http://f.slukjanov.name/jc13/03/ru/sgu/itcourses/Key.class`
Зашифрованный класс: `http://f.slukjanov.name/jc13/03/ru/sgu/itcourses/Secret.class`

```java
import java.net.MalformedURLException;
import java.net.URL;

public class Main {

    public static final String CLASSES_URL = "http://f.slukjanov.name/jc13/03/";

    public static void main(String[] args) throws MalformedURLException, ClassNotFoundException, IllegalAccessException,
            InstantiationException {
        EncryptedClassesClassLoader noEncryptClassLoader = new EncryptedClassesClassLoader(new URL(CLASSES_URL));
        Class<?> secretKeyClass = noEncryptClassLoader.loadClass("ru.sgu.itcourses.Key");
        Object secretKeyInstance = secretKeyClass.newInstance();
        String secretKey = secretKeyInstance.toString();
        System.out.println("secretKey=" + secretKey);

        EncryptedClassesClassLoader encryptedClassesClassLoader = new EncryptedClassesClassLoader(new URL(CLASSES_URL),
                secretKey);
        Class<?> secretClass = encryptedClassesClassLoader.loadClass("ru.sgu.itcourses.Secret");
        Object secretInstance = secretClass.newInstance();
        System.out.println("secretMessage=" + secretInstance.toString());
    }
}
```

Функция шифрования/дешифрования:

```java
byte[] cryptBytes(byte[] bytes, String key) {
        byte[] decryptedBytes = new byte[bytes.length];
        for (int i = 0; i < bytes.length; i++) {
            byte x = (byte) (key.charAt(i % key.length()) - 'a');
            decryptedBytes[i] = (byte) (bytes[i] ^ x);
        }
        return decryptedBytes;
    }
```
