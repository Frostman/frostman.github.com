---                                                                                                                     
layout: page                                                                                                            
title: JC12 - Lecture 03. Concurrency basics                                                                                                                  
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Course 2012 ([back](index.html))
## Lecture 03. Concurrency basics

## План / тезисы

Писать корректно работающие приложения тяжело. 
Писать корректно работающие многопоточные приложения значительно сложнее. 

### Зачем нужна многопоточность?
* увеличение утилизации ресурсов;  
* растет число ядер, а не их частота;
* асинхронность; 
* отзывчивость интерфейсов; 
* распараллеливание разных ресурсов (io и cpu или net и cpu).

### Проблемы

* сложно тестировать;
* синхронизация;
* специфичные проблемы (deadlocks, livelocks, data races);
* safety hazards (non-thread-safe sequence generator).

### Работа с многопоточностью

* заложена в спецификации языка Java;
* синтаксические конструкции;
* четко определённая семантика;
* пакет `java.util.concurent`;
* спецификация Java Memory Model, которая описывает, при каких условиях поток увидит изменения сделанные другим потоком.

### Работа с потоками

* как создать и запустить поток;
* потоки-демоны;
* приоритеты потоков, перезапуск потоков;
* как получить ссылку на текущий поток;
* остановка и приостановка потоков deprecated;
* состояния потоков (NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED);
* ключевое слово `synchronized` на метод/блок/статик метод, при этом происходит захват монитора;
* методы `Thread.sleep()`, `Thread.yield()`,  `Thread#join()`, `Thread.interrupt()`;
* ключевое слово `volatile`;
* `synchronized` и `volatile` также нужны чтобы потоки могли видеть изменения сделанные другими потоками (синхронизацию нельзя использовать только для записи);
* методы `Object#wait()`, `Object#notify()`, `Object#notifyAll()`, при вызове `notify()` просыпается случайный поток, при вызове `wait()` отпускается монитор, захваченный текущим потоком, при вызове `sleep()` и `join()` – нет;
* `IllegalMonitorStateException` при вызове вышеперечисленных методов у объекта, чьим монитором мы не владеем;
* случайные пробуждения (Spurious wakeup), поэтому метод `wait()` нужно звать только в цикле с проверкой условия;
* `InterruptedException`. Просьба прервать поток;
* Deadlocks;
* разница `StringBuffer` / `StringBuilder`, `Vector` / `ArrayList`.

## Практической задание (сделать на занятии)
 
Необходимо реализовать "семафор" с использованием механизма wait-notify.

```java
public class Semaphore {
    public Semaphore(int resourceNumber) {...}

    public void acquire() throws java.lang.InterruptedException {...}
    public void acquire(int number) throws java.lang.InterruptedException {...} 
    public boolean tryAcquire() {...}
    public boolean tryAcquire(int number) {...} 
    public boolean tryAcquire(long timeToWait, java.util.concurrent.TimeUnit timeUnit) throws java.lang.InterruptedException {...}
    public boolean tryAcquire(int number, long timeToWait, java.util.concurrent.TimeUnit timeUnit) throws java.lang.InterruptedException {...}

    public void release() throws InterruptedException {...}
    public void release(int number) throws InterruptedException {...}
}
```

Конструктор принимает на вход число доступных ресурсов, которые можно будет занимать.
Метод `acquire` должен занимать 1 ресурс и если свободного ресурса нет, то ждать его появления.
Метод `acquire(int)` должен занимать N ресурсов, занимая их по возможности и ожидая недостоющих ресурсов.
Методы `tryAcquire` могут не смочь занять ресурсы, в таком случае они должны вернуть false и не занять никаких ресурсов.
`TimeUnit` позволяет указать в каких еденицах указано время (секунды, минуты и тд).

Методы `release` должны освобождать 1 или N ресурсов. Не обязательно делать wait в том случае если при их освобождение будет превышаться число доступных
конструкторов указанных в конструкторе.

## Домашнее задание

*Note.* Все решения должны быть thread-safe.

### Задание #1 

Доделать практическое задание с занятия.

### Задание #2

Первое задание теоретическое. Ответ на каждый вопрос должен занимать 1-3 предложения. 

1. Как получить ссылку на текущий поток?
2. Зачем нужно ключевое слово `synchronized`? На что его можно повесить (поле, метод, класс, конструктор и тд)?
3. Захват какого монитора происходит при входе в `synchronized` метод / статический метод / блок кода?
4. Зачем нужно ключевое слово `volatile`? На что его можно повесить (поле, метод, класс, конструктор и тд)?
5. Что делают методы `Object#wait()`, `Object#notify()`, `Object#notifyAll()`?
6. Когда бросается исключение `IllegalMonitorStateException`?
7. Что делает метод `Thread#join()`?
8. Что делает метод `Thread#interrupt()`?

### Задание #3 

Ваша задача заключается в создании двух реализаций интерфейса `Task`:

```java
public interface Task<T> {
    T get() throws MyException;
}
```

Замените название `MyException` на более подходящее. В это исключение должны оборачиваться исключения 
(в любой из реализаций интерфейса `Task`), возникшие во время исполнения задачи, 
и оно должно быть выброшено всем потокам, которые вызывают `get()`.

Первая реализация - `SimpleTask` должна иметь следующий вид:

```java
import java.util.concurrent.Callable;

public final class SimpleTask<T> implements Task<T> {
    // ...

    public SimpleTask(Callable<? extends T> callable) {
        // ...
    }
    
    public T get() throws MyException {
        // ...
    }
}
```

Метод `get()` должен возвращать результат выполнения `callable` или бросать обернутое исключение,
произошедшее во время выполнения. Если при вызове get() результат уже просчитан, то он должен вернуться сразу
(даже без задержек на вход в синхронизированную область, справедливо для всех реализаций). 
Вычисление значения (выполнение `callable`) должно происходить только один раз - в конструкторе.
`Callable` похож на `Runnuble`, но результатом его работы является объект (а не `void`). 

Далее необходимо реализовать лениво вычисляющийся `LazyTask`, имеющий такой же вид как и `SimpleTask`,
но вычисление значения (выполнение `callable`) должно происходить только при первом обращении к `get()`
(в потоке который совершил первый вызов и только один раз).
   

### Задание #4 

Ваша задача реализовать интерфейс `ExecutionManager`: 

```java
public interface ExecutionManager {
    Context execute(Runnable callback, Runnable... tasks);
}
```

Метод `execute()` принимает массив tasks, это задания которые `ExecutionManager` должен выполнять параллельно
(в вашей реализации это должно происходить в своем пуле потоков). 
После завершения исполнения всех заданий должен выполниться `callback` (ровно 1 раз). 

Метод `execute()` – это неблокирующий метод, который сразу возвращает объект `Context`.
`Context` - это интерфейс следующего вида: 

```java
public interface Context {
    int getCompletedTaskCount(); 
    int getFailedTaskCount(); 
    int getInterruptedTaskCount(); 
    void interrupt(); 
    boolean isFinished(); 
}
```
Описание поведения методов:

* `getCompletedTaskCount()` должен возвращать количество заданий, которые на текущий момент успешно выполнились;
* `getFailedTaskCount()` должен возвращать количество заданий, при выполнении которых было выброшено исключение; 
* `interrupt()` должен отменять выполнения заданий, которые еще не начали выполняться;
* `getInterruptedTaskCount()` должен возвращать количество заданий, которые не были выполены из-за отмены (вызовом предыдущего метода); 
* `isFinished()` должен возвращать `true`, если все таски были выполнены или отменены, `false` в противном случае.  

