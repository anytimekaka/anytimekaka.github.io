---
layout:     post
title:      "Java并发编程——信号量与互量"
date:       2016-03-09 20:00:00
categories: Java
permalink: /archivers/javaconcurrency01
---

信号量用于线程同步，互斥量用户保护资源的互斥访问。

## 信号量与互斥量的区别
 * 互斥量用于线程的互斥，信号线用于线程的同步。  
 * 互斥量值只能为0/1，信号量值可以为非负整数。信号量可以实现多个同类资源的多线程互斥和同步。
 * 互斥量的加锁和解锁必须由同一线程分别对应使用，信号量可以由一个线程释放，另一个线程得到。

## 信号量Semaphore
信号量是在多线程环境中，线程间传递信号的一种方式。

### 简单的Semaphore实现

```java
public class Semaphore {
private boolean signal = false;   //使用signal可以避免信号丢失
public synchronized void take() {
	this.signal = true;
	this.notify();
}
public synchronized void release() throws InterruptedException{
	while(!this.signal) //使用while避免假唤醒
		wait();
	this.signal = false;
	}
}
```

使用场景

```java
Semaphore semaphore = new Semaphore();
SendingThread sender = new SendingThread(semaphore)；
ReceivingThread receiver = new ReceivingThread(semaphore);
receiver.start();
sender.start();

public class SendingThread {
	Semaphore semaphore = null;
	public SendingThread(Semaphore semaphore){
		this.semaphore = semaphore;
	}
	public void run(){
		while(true){
			//do something, then signal
			this.semaphore.take();
		}
	}
}

public class RecevingThread {
	Semaphore semaphore = null;
	public ReceivingThread(Semaphore semaphore){
		this.semaphore = semaphore;
	}
	public void run(){
		while(true){
		this.semaphore.release();
		//receive signal, then do something...
		}
	}
}
```

### 可计数的Semaphore
上面提到的Semaphore的简单实现并没有计算通过调用take方法所产生信号的数量。可以把它改造成具有计数功能的Semaphore。

```java
public class CountingSemaphore {
	private int signals = 0;
	public synchronized void take() {
		this.signals++;
		this.notify();
	}
public synchronized void release() throws InterruptedException{
	while(this.signals == 0) 
		wait();
	this.signals--;
	}
}
```

### 有上限的Semaphore

可以将上面的CountingSemaphore改造成一个信号数量有上限的BoundedSemaphore

```java
public class BoundedSemaphore {
	private int signals = 0;
	private int bound   = 0;
	public BoundedSemaphore(int upperBound){
		this.bound = upperBound;
	}
	public synchronized void take() throws InterruptedException{
		while(this.signals == bound) 
			wait();
		this.signals++;
		this.notify();
	}
	public synchronized void release() throws InterruptedException{
		while(this.signals == 0) 
			wait();
		this.signals--;
		this.notify();
	}
}
```

在BoundedSemaphore中，当已经产生的信号数量达到了上限，take方法将阻塞新的信号产生请求，直到某个线程调用release方法后，被阻塞于take方法的线程才能传递自己的信号。

### Java内置的Semaphore

java.util.concurrent包中有Semaphore的实现，可以设置参数，控制同时访问的个数。
下面的Demo中申明了一个只有5个许可的Semaphore，而有20个线程要访问这个资源，通过acquire()和release()获取和释放访问许可。

```java
final Semaphore semp = new Semaphore(5);
ExecutorService exec = Executors.newCachedThreadPool();
for (int index = 0; index < 20; index++) {
	final int NO = index;
	Runnable run = new Runnable() {
		public void run() {
			try {
				// 获取许可
				semp.acquire();
				System.out.println("Accessing: " + NO);
				Thread.sleep((long) (Math.random() * 10000));
				// 访问完后，释放
				semp.release();
				System.out.println("-----------------" + semp.availablePermits());
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	};
	exec.execute(run);
}
exec.shutdown();
```

## 互斥量Mutex
互斥量：提供对资源的独占访问，只能为0/1，如果某一个资源同时只能允许一个访问者对其访问，可以使用互斥量控制线程对其访问。

互斥量实现：

```java
public class Mutex {
private boolean isLocked = false;
public synchronized void lock() {
	while(this.isLocked) //使用while可以避免线程 假唤醒
		wait();
	this.isLocked= true;
	}
}
public synchronized void unlock() throws InterruptedException{
	this.isLocked= false;
	this.notify();
	}
}
```

在Mutex中，我们添加了一个signal用于保存信号。

将互斥量当作**锁**来使用：

```java
Mutex mutex = new Mutex();
mutex.lock();
...
//临界区
mutex.unlock();
```

互斥量的加锁和解锁必须由同一个线程分别对应使用。

## 参考

* [信号量-并发编程网](http://ifeve.com/semaphore/)
* [信号量与互斥量-博客园](http://www.cnblogs.com/diyingyun/archive/2011/12/04/2275229.html)
* [Java信号量-博客园](http://www.cnblogs.com/whgw/archive/2011/09/29/2195555.html)
