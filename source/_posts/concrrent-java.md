---
title: LeetCode多线程题所引发的...
date: 2019/07/28
categories: 
    - Java
---

## context

LeetCode[1114. Print in Order](https://leetcode.com/problems/print-in-order/)
多线程题目

## 解法汇总

### synchronized

性能差点

```java
class Foo {
    
    private boolean onePrinted;
    private boolean twoPrinted;
    
    public Foo() {
        onePrinted = false;
        twoPrinted = false;
    }

    public synchronized void first(Runnable printFirst) throws InterruptedException {
        
        printFirst.run();
        onePrinted = true;
        notifyAll();
    }

    public synchronized void second(Runnable printSecond) throws InterruptedException {
    
        while(!onePrinted){
            wait();
        }
        printSecond.run();
        twoPrinted = true;
        notifyAll();
    }

    public synchronized void third(Runnable printThird) throws InterruptedException {
        
        // printThird.run() outputs "third". Do not change or remove this line.
        while(!twoPrinted){
            wait();
        }
        printThird.run();
    }
}
```

### [CAS](https://juejin.im/post/5a73cbbff265da4e807783f5)

原子操作一个变量，用这个变量来标记是否执行

```java
import java.util.concurrent.atomic.AtomicInteger;
class Foo {

    AtomicInteger count = new AtomicInteger();
    public Foo() {
        count.set(0);
    }

    public void first(Runnable printFirst) throws InterruptedException {
        
        // printFirst.run() outputs "first". Do not change or remove this line.
        while(!count.compareAndSet(0, 1)){}
        printFirst.run();
    }

    public void second(Runnable printSecond) throws InterruptedException {
        
        // printSecond.run() outputs "second". Do not change or remove this line.
        while(!count.compareAndSet(1,2)){}
        printSecond.run();
    }

    public void third(Runnable printThird) throws InterruptedException {
        
        // printThird.run() outputs "third". Do not change or remove this line.
        while(!count.compareAndSet(2,3)){}
        printThird.run();
    }
}
```

### [CountDownLatch](https://www.cnblogs.com/dolphin0520/p/3920397.html)

一个线程等待其他活动都完成后才执行

使用构造函数，声明需要等待多少个活动

使用countdown将这个要等待的活动数目减少

调用await()方法的线程会被挂起，它会等待直到count值为0才继续执行

```java
import java.util.concurrent.CountDownLatch;
class Foo {

    CountDownLatch secondLatch;
    CountDownLatch thirdLatch;

    public Foo() {
        this.secondLatch = new CountDownLatch(1);
        this.thirdLatch = new CountDownLatch(1);
    }

    public void first(Runnable printFirst) throws InterruptedException {
        
        // printFirst.run() outputs "first". Do not change or remove this line.
        printFirst.run();
        this.secondLatch.countDown();
    }

    public void second(Runnable printSecond) throws InterruptedException {
        
        // printSecond.run() outputs "second". Do not change or remove this line.
        this.secondLatch.await();
        printSecond.run();
        this.thirdLatch.countDown();
    }

    public void third(Runnable printThird) throws InterruptedException {
        
        // printThird.run() outputs "third". Do not change or remove this line.
        this.thirdLatch.await();
        printThird.run();
    }
}
```

### Semaphore

信号量标记可用资源数目（许可数目）

acquire申请一个许可

release释放一个许可

```
import java.util.concurrent.*;
class Foo {
    
    Semaphore run2, run3;
    
    public Foo() {
        run2 = new Semaphore(0);
        run3 = new Semaphore(0);
    }

    public void first(Runnable printFirst) throws InterruptedException {
        
        // printFirst.run() outputs "first". Do not change or remove this line.
        printFirst.run();
        run2.release();
    }

    public void second(Runnable printSecond) throws InterruptedException {
        
        // printSecond.run() outputs "second". Do not change or remove this line.
        run2.acquire();
        printSecond.run();
        run3.release();
    }

    public void third(Runnable printThird) throws InterruptedException {
        
        // printThird.run() outputs "third". Do not change or remove this line.
        run3.acquire();
        printThird.run();
    }
}
```
