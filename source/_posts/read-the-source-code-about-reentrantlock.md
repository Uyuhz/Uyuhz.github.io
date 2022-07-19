---
title: 源码阅读之ReentrantLock
date: 2022-04-22 14:00:28
tags: Java
categories: 编程
---
本文主要从ReentrantLock非公平模式下的加锁过程讲起。

<!-- more -->
# ReentrantLock的结构
ReentrantLock类中包含一个名为`Sync`的抽象类，继承于`java.util.concurrent.locks.AbstractQueuedSynchronizer`，并为公平锁和非公平锁提供了不同的实现：`FairSync`和`NonfairSync`。
```java
public class ReentrantLock implements Lock, java.io.Serializable {

    private final Sync sync;

    abstract static class Sync extends AbstractQueuedSynchronizer {
        // ...
    }

    static final class NonfairSync extends Sync {
        // ...
    }

    static final class FairSync extends Sync {
        // ...
    }
```

## AbstractQueuedSynchronizer
> AQS 是一个用来构建锁和同步器的框架，使用 AQS 能简单且高效地构造出应用广泛的大量的同步器， 比如我们提到的 ReentrantLock，Semaphore，其他的诸如 ReentrantReadWriteLock，SynchronousQueue，FutureTask(jdk1.7) 等等皆是基于 AQS 的。


